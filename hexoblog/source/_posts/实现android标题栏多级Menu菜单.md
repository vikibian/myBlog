---
title: 实现android标题栏多级Menu菜单
date: 2020-05-07 18:59:34
thumbnail: /image/20200507/050701.jpg
categories: android
tags: [android,menu]
---
实现android标题栏的多级menu菜单
<!-- more -->
效果图如下：

[![https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200507/GtjmT9ud6uzW.png](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200507/GtjmT9ud6uzW.png)](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200507/GtjmT9ud6uzW.png)

图中的第一条点击展开。还会显示其他很多项。

### 1:设置Xml文件布局

编程设置多级menu菜单

```java
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:title="ListView效果"
        android:id="@+id/list_view">
        <menu>
            <item android:id="@+id/list_view_vertical_standrad"
                android:title="垂直标准">
            </item>
            <item android:id="@+id/list_view_vertical_reverse"
                android:title="垂直反向">
            </item>
            <item android:id="@+id/list_view_horizontal_standrad"
                android:title="水平标准">
            </item>
            <item android:id="@+id/list_view_horizontal_reverse"
                android:title="水平反向">
            </item>
        </menu>

    </item>
    <item android:title="GridView效果"
        android:id="@+id/grid_view">

    </item>

    <item android:title="瀑布流效果"
        android:id="@+id/stagger_view">

    </item>

</menu>
```



### 2:配置ID与事件

在需要调用的activity界面中重写onCreateOptionsMenu方法和onOptionsItemSelected方法

```java
 	//调用创建的menu菜单
   @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.testtrillgates,menu);
        return super.onCreateOptionsMenu(menu);

    }

	//为menu菜单配置点击事件
    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        int itemId = item.getItemId();
        switch (itemId){
            case R.id.list_view_vertical_standrad:
                //执行操作
                break;
            default:
                break;
        }

        return super.onOptionsItemSelected(item);
    }
```

