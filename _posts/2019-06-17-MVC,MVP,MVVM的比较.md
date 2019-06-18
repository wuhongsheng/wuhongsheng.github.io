---
layout: post
title:  "MVC,MVP,MVVM的比较"
date:   2019-06-17 08:14:54
categories: Android
tags:  Android
author: WHS
---

* content
{:toc}

之前看过很多关于Android MVVM的博客，但大多数提到的都是DataBinding的基本用法，很少有文章仔细讲解在Android中是如何通过DataBinding去构建MVVM的应用框架的。View、ViewModel、Model每一层的职责如何?它们之间联系怎样、分工如何?





### MVC,MVP,MVVM的比较
1. MVC
传统的Android App其实都是基于MVC的，Activity，Fragment相当于C,布局相当于V,数据逻辑相当于M
随着业务的增长Controller里的代码会越来越臃肿，因为它不只要负责业务逻辑，还要控制View的展示。也就是说Activity、Fragment杂糅了Controller和View，耦合变大。并不能算作真正意义上的MVC。

![](http://www.jcodecraeer.com/uploads/20160414/1460565635729862.png)

2. MVP
MVP架构其实可以说与MVC的架构还是有很大的差别的，数据逻辑相当于M，Activity（负责View的绘制以及与用户交互）相当于V ，View于Model间的交互则为P其实最明显的区别就是，MVC中是允许Model和View进行交互的，而MVP中很明显，Model与View之间的交互由Presenter完成。还有一点就是Presenter与View之间的交互是通过接口的

![](http://www.jcodecraeer.com/uploads/20160414/1460565637114968.png)

3. MVVM
MVVM是Model-View-ViewModel的简写. 它是有三个部分组成：Model、View、ViewModel。Model：数据模型层。包含业务逻辑和校验逻辑,View：屏幕上显示的UI界面（layout、views）,ViewModel：View和Model之间的链接桥梁，处理视图逻辑。
当View有用户输入后，ViewModel通知Model更新数据，同理Model数据更新后，ViewModel通知View更新。


### MVVM其实与MVP架构看起来很相似
![](https://upload-images.jianshu.io/upload_images/1605450-40b99f4f565fe170.png?imageMogr2/auto-orient/)

### 代码示例

* MVVM

```java
/**
 * @author whs
 * @date 2019/3/29
 * 清障救援
 */
public class RescueActivity extends WtActivity {
    //接单ID
    public static final String RESCUEID= "RESCUEID";
    private ActivityRescueBinding mBinding;
    private RescueViewModel mViewModel;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_rescue);
        mBinding.header.toolbar.setNavigationOnClickListener(view -> onBackPressed());
        mBinding.header.tvTitle.setText(getString(R.string.rescue));
        mBinding.header.ivRight.setVisibility(View.VISIBLE);
        mBinding.header.ivRight.setImageResource(R.mipmap.orders);
        mBinding.header.ivRight.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if(mViewModel.mTaskNum.getValue() > 0){
                    Intent intent = new Intent(RescueActivity.this, ClearingActivity.class);
                    startActivity(intent);
                }else {
                    mViewModel.message.setValue("暂无新任务");
                }
            }
        });
        mViewModel = ViewModelProviders.of(this).get(RescueViewModel.class);
        init(mViewModel);
        
        mViewModel.loadClearTaskNum();

        // Check that the activity is using the layout version with
        // the fragment_container FrameLayout
        if (mBinding.flContent != null) {
            // However, if we're being restored from a previous state,
            // then we don't need to do anything and should return or else
            // we could end up with overlapping fragments.
            if (savedInstanceState != null) {
                return;
            }
            getSupportFragmentManager().beginTransaction()
                    .add(R.id.fl_content, TaskFragment.getFragment()).commit();


        }
        
    }

    private void replaceFragment(BaseFragment fragment) {
        getSupportFragmentManager().beginTransaction().replace(R.id.fl_content, fragment).commit();
    }

}


/**
 * Created by whs on 2019/3/27
 * 请障救援
 */
public class RescueViewModel extends BaseViewModel {

    /**请障救援任务数量*/
    public MutableLiveData<Integer> mTaskNum = new MutableLiveData<>();

    /**
     * 获取清障救援任务数
     */
    public void loadClearTaskNum() {
        webApi.getClearRescueTaskNum().enqueue(new BaseCallBack<Integer>("救援任务",this) {
            @Override
            public void onResponse(Integer data) throws UnsupportedEncodingException {
                mTaskNum.setValue(data);
            }
        });
    }
}

```

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <import type="android.view.View" />
        <import type="androidx.core.content.ContextCompat" />
        <variable
            name="viewModel"
            type="com.wtkj.baseproduct.ui.rescue.RescueViewModel"/>
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <include
            android:id="@+id/header"
            layout="@layout/toolbar_common" />

        <TextView
            android:id="@+id/tv_notify"
            android:layout_width="20dp"
            android:layout_height="20dp"
            android:layout_gravity="right"
            android:layout_margin="10dp"
            android:background="@drawable/background_notify_circle_red"
            android:gravity="center"
            android:textColor="@color/white"
            android:textSize="12sp"
            isVisiable="@{viewModel.mTaskNum > 0}"
            android:text="@{String.valueOf(viewModel.mTaskNum)}"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <FrameLayout
            android:id="@+id/fl_content"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/header" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```


