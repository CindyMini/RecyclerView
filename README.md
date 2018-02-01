

 先看总体效果图
 
![效果图](https://github.com/linglongxin24/RecyclerViewDemo2/blob/master/images/effect.gif?raw=true)

#一.RecyclerView介绍

RecyclerView 是Android 5.0版本中新出现的东西，用来替代ListView和GridView的。

#二.RecyclerView架构

![架构](https://github.com/linglongxin24/RecyclerViewDemo2/blob/master/images/introduce.png?raw=true)

#三.RecyclerView的优点

 * 1.RecyclerView内部已经强制使用VIewHolder，优化了对Item中View的复用。
 
 * 2.提供了一个较为灵活的布局管理类，LayoutManager，用来控制RecyclerView是线性展示还是垂直展示，以及可以设置成瀑布流。并且提供了ItemDecoration这个类，来灵活的让用户来设置分割线。

 * 3.用ItemAnimator可以对item进行布局的删减动画效果。
 
#四.RecyclerView实现三部曲

 *  第一步：设置布局管理器
 
 用来确定每一个item如何进行排列摆放:     
  *  LinearLayoutManager 相当于ListView的效果   
  *  GridLayoutManager相当于GridView的效果      
  *  StaggeredGridLayoutManager 瀑布流
 
 *  第二步：添加分割线
 
 我们用到了网上流传的万能分割线DividerItemDecoration和DividerGridItemDecoration，首先在style.xml里面定义分割线图片：
 
	1.先在drawable中新建divider.xml  
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle" >
    <solid android:color="@color/colorAccent"/>
    <size android:height="1dp"
        android:width="1dp"/>
</shape>
```

		2.然后在style.xml中设置android:listDivider


```xml
        <item name="android:listDivider">@drawable/divider</item>
```

  *  第三步：设置适配器
 
```java
package cn.bluemobi.dylan.recyclerviewdemo2;

import android.content.Context;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

import java.util.ArrayList;
import java.util.List;

/**
 * RecyclerView的适配器
 * Created by yuandl on 2016-11-01.
 */

public class RvAdapter extends RecyclerView.Adapter<RvAdapter.MyViewHolder> {
    private Context context;
    private List<Integer> datas;

    /**
     * item的点击事件的长按事件接口
     */
    private OnItemClickListener onItemClickListener;
    /**
     * 瀑布流时的item随机高度
     */
    private List<Integer> heights = new ArrayList<>();

    /**
     * 不同的类型设置item不同的高度
     *
     * @param type
     */

    private int type = 0;

    public RvAdapter(Context context, List<Integer> datas) {
        this.context = context;
        this.datas = datas;
        for (int i : datas) {
            int height = (int) (Math.random() * 100 + 300);
            heights.add(height);
        }
    }

    public void setType(int type) {
        this.type = type;
    }

    /**
     * 设置点击事件
     *
     * @param onItemClickListener
     */
    public void setOnItemClickListener(OnItemClickListener onItemClickListener) {
        this.onItemClickListener = onItemClickListener;
    }

    @Override
    public MyViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View contentView = LayoutInflater.from(context).inflate(R.layout.item, parent, false);
        MyViewHolder viewHolder = new MyViewHolder(contentView);
        return viewHolder;
    }

    @Override
    public void onBindViewHolder(MyViewHolder holder, final int position) {
        RecyclerView.LayoutParams layoutParams;
        if (type == 0) {
            layoutParams = new RecyclerView.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT);
        } else if (type == 1) {
            layoutParams = new RecyclerView.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
        } else {
            layoutParams = new RecyclerView.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, heights.get(position));
            layoutParams.setMargins(2, 2, 2, 2);
        }
        holder.itemView.setLayoutParams(layoutParams);
        holder.imageView.setImageResource(datas.get(position));
        holder.tv.setText("分类" + position);
        /**设置item点击监听**/
        holder.itemView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (onItemClickListener != null) {
                    onItemClickListener.onItemClickListener(position, datas.get(position));
                }
            }
        });

    }

    @Override
    public int getItemCount() {
        return datas == null ? 0 : datas.size();
    }

    /**
     * 用于缓存的ViewHolder
     */
    public class MyViewHolder extends RecyclerView.ViewHolder {
        private ImageView imageView;
        public TextView tv;

        public MyViewHolder(View itemView) {
            super(itemView);
            imageView = (ImageView) itemView.findViewById(R.id.iv);
            tv = (TextView) itemView.findViewById(R.id.tv);
        }
    }

    /**
     * 设置item监听的接口
     */
    public interface OnItemClickListener {
        void onItemClickListener(int position, Integer data);

    }
}

```
#五.打造各种效果

 * 1.竖向的ListView

```java
    rv.setBackgroundColor(Color.TRANSPARENT);
    rv.setLayoutManager(new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false));
    rvAdapter.setType(0);
    rv.removeItemDecoration(itemDecoration);
    rv.setLayoutParams(new RelativeLayout.LayoutParams(RelativeLayout.LayoutParams.MATCH_PARENT, RelativeLayout.LayoutParams.MATCH_PARENT));
    itemDecoration = new DividerItemDecoration(this, DividerItemDecoration.VERTICAL_LIST);
    rv.addItemDecoration(itemDecoration);
```

![效果图](https://github.com/linglongxin24/RecyclerViewDemo2/blob/master/images/listview.png?raw=true)

 * 2.横向的ListView

```java
   rvAdapter.setType(1);
   rv.removeItemDecoration(itemDecoration);
   rv.setLayoutParams(new RelativeLayout.LayoutParams(RelativeLayout.LayoutParams.MATCH_PARENT, RelativeLayout.LayoutParams.WRAP_CONTENT));
   rv.setLayoutManager(new LinearLayoutManager(this, LinearLayoutManager.HORIZONTAL, false));
   itemDecoration = new DividerItemDecoration(this, DividerItemDecoration.HORIZONTAL_LIST);
   rv.addItemDecoration(itemDecoration);
```

![效果图](https://github.com/linglongxin24/RecyclerViewDemo2/blob/master/images/horizontalListView.png?raw=true)
 
 
 * 3.竖向的GridView

```java
   rvAdapter.setType(1);
   rv.setBackgroundColor(Color.TRANSPARENT);
   rv.removeItemDecoration(itemDecoration);
   rv.setLayoutParams(new RelativeLayout.LayoutParams(RelativeLayout.LayoutParams.MATCH_PARENT, RelativeLayout.LayoutParams.MATCH_PARENT));
   rv.setLayoutManager(new GridLayoutManager(this, 5));
   itemDecoration = new DividerGridItemDecoration(this);
   rv.addItemDecoration(itemDecoration);
```

![效果图](https://github.com/linglongxin24/RecyclerViewDemo2/blob/master/images/gridview.png?raw=true)
  
 * 4.横向的GridView

```java
    rvAdapter.setType(1);
    rv.setBackgroundColor(Color.TRANSPARENT);
    rv.setLayoutParams(new RelativeLayout.LayoutParams(RelativeLayout.LayoutParams.MATCH_PARENT, RelativeLayout.LayoutParams.WRAP_CONTENT));
    rv.removeItemDecoration(itemDecoration);
    rv.setLayoutManager(new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.HORIZONTAL));
    itemDecoration = new DividerGridItemDecoration(this);
    rv.addItemDecoration(itemDecoration);
```

![效果图](https://github.com/linglongxin24/RecyclerViewDemo2/blob/master/images/horizontalGridView.png?raw=true)
  
 * 5.竖向的瀑布流

```java
     rvAdapter.setType(3);
     rv.setBackgroundColor(getResources().getColor(R.color.colorAccent));
     rv.setLayoutParams(new RelativeLayout.LayoutParams(RelativeLayout.LayoutParams.MATCH_PARENT, RelativeLayout.LayoutParams.MATCH_PARENT));
     rv.removeItemDecoration(itemDecoration);
     rv.setLayoutManager(new StaggeredGridLayoutManager(5, StaggeredGridLayoutManager.VERTICAL));
```

![效果图](https://github.com/linglongxin24/RecyclerViewDemo2/blob/master/images/staggeredgridview.png?raw=true)

 * 6.添加和删除动画

```java
       /**添加一个数据**/
       rvAdapter.notifyItemInserted(1);
       /**删除一个数据**/
       rvAdapter.notifyItemRemoved(1);
```

![效果图](https://github.com/linglongxin24/RecyclerViewDemo2/blob/master/images/add_delete_effect.gif?raw=true)

#六.[GiHub](https://github.com/linglongxin24/RecyclerViewDemo2)
