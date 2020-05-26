---
title: 在子fragment实现TabLayout和ViewPager的组合
date: 2020-05-09 22:28:18
thumbnail: /image/20200509/050901.jpg
categories: android
tags: [android,Fragment,TabLayout,ViewPager]
---
 在子fragment实现TabLayout和ViewPager的组合
 <!-- more -->

## 1：问题描述

在软件有一个主界面FragmentManagerActivity，在主界面的下面实现了BottomBar功能状态栏，点击每一项时会加载相应的Fragment，根据要求，需要在某一个fragment中实现，TabLayout和ViewPager的结合效果，如图所示：

[![https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200509/7s!wkm1Y9Z9N.png](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200509/7s!wkm1Y9Z9N.png)](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200509/7s!wkm1Y9Z9N.png)



## 2：代码实现

（1）：首先实现点击下面第3个fragment的xml文件的代码编写，布局为fragment_temporary_task。

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:id="@+id/draft_box_scrollview"
    tools:context=".fragment.TemporaryTaskFragment">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:background="@color/qmui_config_color_white">

        <com.google.android.material.tabs.TabLayout
            android:id="@+id/fragment_temp_tabLayout"
            android:layout_width="match_parent"
            android:layout_height="30dp"
            app:tabIndicatorColor="@color/lightblue"
            app:tabTextColor="@color/gray"
            app:tabSelectedTextColor="@color/lightblue"
            app:tabTextAppearance="@style/TabLayoutTextStyle"
            app:tabMode="fixed"
            app:tabBackground="@drawable/selector_bg">

        </com.google.android.material.tabs.TabLayout>

        <androidx.viewpager.widget.ViewPager
            android:id="@+id/fragment_temp_viewpager"
            android:layout_weight="1"
            android:layout_width="match_parent"
            android:layout_height="0dp">

        </androidx.viewpager.widget.ViewPager>

    </LinearLayout>


</LinearLayout>
```

对应的Fragment文件

```java
public class TemporaryTaskFragment extends Fragment  {

    private static final String TAG = "TemporaryTaskFragment";
    private QMUITabSegment tabSegment;

    private TabLayout mTabLayout;
    private ViewPager mViewPager;
    private TemporaryTaskFragmentAdapter myFragmentPagerAdapter;
    private PromptDialog promptDialog;
    private List<Fragment> fragments = new ArrayList<>();

    private TabLayout.Tab one;
    private TabLayout.Tab two;


    public TemporaryTaskFragment() {
        // Required empty public constructor
    }


    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        Log.e(TAG,"onCreateView");
        // Inflate the layout for this fragment
        View view = inflater.inflate(R.layout.fragment_temporary_task, container, false);
        AppCompatActivity activity = (AppCompatActivity) getActivity();
        SimpleToolbar simple_toolbar = activity.findViewById(R.id.simple_toolbar);
        simple_toolbar.setVisibility(View.VISIBLE);
        promptDialog = new PromptDialog(getActivity());

        initview(view);
 

        return view;
    }


    private void initview(View view) {

        //使用适配器将ViewPager与Fragment绑定在一起
        mViewPager = view.findViewById(R.id.fragment_temp_viewpager);
        myFragmentPagerAdapter = new TemporaryTaskFragmentAdapter(getChildFragmentManager());
        mViewPager.setAdapter(myFragmentPagerAdapter);

        //将TabLayout与ViewPager绑定在一起
        mTabLayout = view.findViewById(R.id.fragment_temp_tabLayout);
        mTabLayout.setupWithViewPager(mViewPager);

        //指定Tab的位置
        one = mTabLayout.getTabAt(0);
        two = mTabLayout.getTabAt(1);

    }
    
}
```

注：如果此fragment是activity则需要将TemporaryTaskFragmentAdapter()中传入getActivity().getSupportFragmentManager()，此处传入getChildFragmentManager()是因为在fragment内含有很多个fragment，又在fragment中嵌套了子fragment，如果设置为前者而不是getChildFragmentManager()，则会出现在滑动viewpager时出现错误。

（2）：编写ViewPager的Adapter文件

```java
public class TemporaryTaskFragmentAdapter extends FragmentPagerAdapter {
    private static final String TAG = "TemporaryTaskFragmentAd";
    private final String[] mTitles = {"任务起草","隐患上报"};

    public TemporaryTaskFragmentAdapter(@NonNull FragmentManager fm) {
        super(fm);
    }

    @NonNull
    @Override
    public Fragment getItem(int position) {
        Log.e(TAG,"position:"+position);
        if (position == 0){
            return new DraftTaskFragment();
        }else if (position == 1){
            return new CheckDetailsVideoFragment();
        }
        return new DraftTaskFragment();
    }

    @Override
    public int getCount() {
        return mTitles.length;
    }

    //ViewPager与TabLayout绑定后，这里获取到PageTitle就是Tab的Text
    @Override
    public CharSequence getPageTitle(int position) {
        return mTitles[position];
    }

}
```



这样在滑动时，就会跳转到相应的“任务起草”Fragment或“隐患上报”Fragment，而不会出现错误。





## 3.后续

但是在实际的使用过程中发现，当fragment先跳转到“我的”fragment即下面的图片

[![https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/sGgHd*nwC0F1.png](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/sGgHd*nwC0F1.png)](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/sGgHd*nwC0F1.png)

然后在点击下面左手数第三个图标就会出现下图的错误。

[![https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/RZk@vlXU5J*K.png](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/RZk@vlXU5J*K.png)](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/RZk@vlXU5J*K.png)

下面的bottombar就会消失，但是左右可以正常滑动，当点击上面页面中的某一个editetxt时，bottombar就会正常出现，如下图所示：

[![https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/1ibcg!IwaAMK.png](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/1ibcg!IwaAMK.png)](https://gitee.com/vikibian/myimagebed/raw/master/PictureStorage/20200512/1ibcg!IwaAMK.png)

“我的”界面的fragment是使用了QMUI的List的布局。出现这个bug后之后，首先分析可能是fragmentmanager中出现了多余的fragment，但是发现不对。其次，我又觉得可能是使用的xml的布局文件出现了问题，经过一段时间测试，发现也不是问题的根源所在。最后，我想起之前想尝试QMUI中的QMUITabSegment控件时，看了看QMUI的源码，发现QMUI中在使用Tab时，使用Tab的addOnTabSelectedListener属性，用来判断tab的滑动，就顺着这个思路试着看看能不能通过这个方法来切换fragment，经过尝试，就在xml文件的viewpager控件中，添加了一个fragmelayout布局，使其通过viewpager来切换fragment。源码如下

（1）：fragment_temporary_task.xml 修改为

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:id="@+id/draft_box_scrollview"
    tools:context=".fragment.TemporaryTaskFragment">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:background="@color/qmui_config_color_white">

        <com.google.android.material.tabs.TabLayout
            android:id="@+id/fragment_temp_tabLayout"
            android:layout_width="match_parent"
            android:layout_height="30dp"
            app:tabMaxWidth="0dp"
            app:tabGravity="fill"
            app:tabIndicatorColor="@color/lightblue"
            app:tabTextColor="@color/gray"
            app:tabSelectedTextColor="@color/lightblue"
            app:tabTextAppearance="@style/TabLayoutTextStyle"
            app:tabMode="fixed"
            app:tabBackground="@drawable/selector_bg">

        </com.google.android.material.tabs.TabLayout>

        <androidx.viewpager.widget.ViewPager
            android:id="@+id/fragment_temp_viewpager"
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <FrameLayout
                android:id="@+id/fl_content_temp"
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight="1"
                />

        </androidx.viewpager.widget.ViewPager>

    </LinearLayout>


</LinearLayout>

```

（2）：与上面xml文件对应的fragment中代码修改为：

```java
public class TemporaryTaskFragment extends Fragment  {

    private static final String TAG = "TemporaryTaskFragment";
    private QMUITabSegment tabSegment;

    private TabLayout mTabLayout;
    private ViewPager mViewPager;
    private TemporaryTaskFragmentAdapter myFragmentPagerAdapter;
    private PromptDialog promptDialog;

    private TabLayout.Tab one;
    private TabLayout.Tab two;
    private FrameLayout frameLayout;
    private List<Fragment> mFragmentList = new ArrayList<>();

    public TemporaryTaskFragment() {
        // Required empty public constructor
    }


    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        View view = inflater.inflate(R.layout.fragment_temporary_task, container, false);
        promptDialog = new PromptDialog(getActivity());

        initview(view);

        return view;
    }


    private void initview(View view) {
//        tabSegment = view.findViewById(R.id.fragment_temp_tab);
        mFragmentList.clear();
        mFragmentList.add(new DraftTaskFragment());
        mFragmentList.add(new YinhuanFragment());
        frameLayout = view.findViewById(R.id.fl_content_temp);
        //使用适配器将ViewPager与Fragment绑定在一起
        mViewPager = view.findViewById(R.id.fragment_temp_viewpager);

        //将TabLayout与ViewPager绑定在一起
        mTabLayout = view.findViewById(R.id.fragment_temp_tabLayout);
        mTabLayout.setupWithViewPager(mViewPager);

        //指定Tab的位置
        one = mTabLayout.getTabAt(0);
        two = mTabLayout.getTabAt(1);
        changeFragment(0);

        mTabLayout.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
            @Override
            public void onTabSelected(TabLayout.Tab tab) {
                Log.e(TAG,"postion:"+tab.getPosition());
                changeFragment(tab.getPosition());
            }

            @Override
            public void onTabUnselected(TabLayout.Tab tab) {

            }

            @Override
            public void onTabReselected(TabLayout.Tab tab) {

            }
        });

    }

    private void changeFragment(int position) {
        FragmentTransaction transaction = getActivity().getSupportFragmentManager().beginTransaction();
        transaction.replace(R.id.fl_content_temp, mFragmentList.get(position));
        transaction.commit();
    }


  @Override
  public void onActivityCreated(@Nullable Bundle savedInstanceState) {
    super.onActivityCreated(savedInstanceState);
    myFragmentPagerAdapter = new TemporaryTaskFragmentAdapter(getChildFragmentManager());
    mViewPager.setAdapter(myFragmentPagerAdapter);
  }
```

这样上面出现的bug就解决了。