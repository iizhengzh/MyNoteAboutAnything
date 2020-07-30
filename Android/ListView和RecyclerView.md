ListView 和RecyclerView

1.ListView:

​	1.1使用：listview的简单使用即在layout中写下一个listview，然后再获取这个listview，new一个adapter，传入上下文，数据list以及单条数据的布局layout（如果是自定义adapter的话根据写好的构造函数而定，可能仅需传入上下文和list），最后用listview.setAdapter(adapter)即可。

​	1.2 提高效率：通过新建一个内部类ViewHolder将子项的各个控件作为成员属性，重写getView方法，对传入该方法的参数convertView进行判断，如果为null则进行布局和控件的加载，并将viewHolder通过setTag存储到view中，如果不为null则直接view.getTag():

`	if(convertView == null){
            view = LayoutInflater.from(getContext()).inflate(resourceId,parent,false);
            viewHolder = new ViewHolder();
            viewHolder.personImage = view.findViewById(R.id.person_image);
            viewHolder.personTextView = view.findViewById(R.id.person_name);
            view.setTag(viewHolder);//将ViewHolder存储到View中
        }else{
            view = convertView;
            viewHolder = (ViewHolder) view.getTag();
        }`

​	1.3点击事件：如果是整个子项的点击事件只需要对listview设置

`listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {}});`

​	如果是对子项中的控件进行点击事件，则在adapter中对控件进行设置即可

----------------------------------------------------

2.RecyclerView:

​	https://www.jianshu.com/p/b4bb52cdbeb7

​	2.1使用：首先添加依赖库

` implementation 'com.android.support:recyclerview-v7:27.1.1'`

并sync now同步，在layout中添加recyclerview。编写适配器adapter让它继承RecyclerView.Adapter<>将其中的泛型指定为adapter.ViewHolder。

​	定义内部类继承RecyclerView.ViewHolder。传入的参数通常是RecyclerView子项的最外层布局。构造函数接受数据源。由于它继承于RecyclerView.Adapter，所以必须重写`onCreateViewHolder()`,`onBindViewHolder()`和`getItemCount()`三个方法，第一个方法用于创建ViewHolder实例并把加载的布局传入构造函数并返回holder，第二个方法则是对每个子项赋值，第三个方法返回recylerview的子项数目。

​	和listview的使用相似，获取recyclerview后setAdapter，但在此之前需要使用layoutManager指定布局方式

```java
LinearLayoutManager layoutManager = new LinearLayoutManager(this);
        recyclerView.setLayoutManager(layoutManager);
```

LineatLayoutManager指的是线性布局。

​	2.2 布局

​	RecyclerView还可以实现横向滚动，只需要将子项的布局进行修改，即不要使宽度占满屏幕，其他根据需要调整，然后再使用时候设置layoutManager时写

```java
LinearLayoutManager layoutManager = new LinearLayoutManager(this);
layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);
recyclerView.setLayoutManager(layoutManager);
```

​	除了`LinearLayoutManager`,`RecyclerView`还提供了`GridLayoutManager(网格布局)`和`StaggeredGridLayoutManager(瀑布流布局)`

```java
GridLayoutManager layoutManager = new GridLayoutManager(this,5);
```

​	设置网格布局的代码如上，第二个参数是网格的列数。瀑布流参见上面链接。

​	2.3点击事件

​	在viewHolder中添加保存最外层view，在onCreatViewHolder中通过holder.view.setOnClickListener进行设置子项的点击事件，其他子项中的控件也在这里进行设置。

3. 两者对比：

   ListView布局单一，RecyclerView可以有横向，瀑布和表格布局

   自定义适配器中，ListView适配器继承ArrayAdapter，RecyclerView是继承RecycleAdapter并将泛型指定为子项对象类.ViewHolder

   ListView优化需要自定义ViewHolder和判断convertView是否为null

   

   https://blog.csdn.net/qq_39899087/article/details/86513378