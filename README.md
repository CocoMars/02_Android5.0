# 02_Android5.0
 Android
 分别为主题编辑器 动态切换主题    
 
 1. 首先，先在styles.xml文件夹下定义一堆样式
 
 2. 在Activity中定义一个成员变量，来标识当前默认显示的主题
 
 3. 在Activity的onCreate方法中通过系统的“setTheme()”方法来设置当前Activity要显示的主题。
 
  ******注意：必须在“super.onCreate()” 方法执行之前调用“setTheme()”方法：



                   @Override
                   protected void onCreate(Bundle savedInstanceState) {
                       if (mTheme != -1) {
                           setTheme(mTheme);
                       }
                       super.onCreate(savedInstanceState);
                       ……
                   }
4. 在更改主题时，根据被更改的主题，设置mTheme的值：


      private void setTheme(int index) {
       switch (index) {
          case DEFAULT_THEME:
             MainActivity.mTheme = R.style.DefaultTheme;
             break;
          case DEFAULT_LIGHT_THEME:
             MainActivity.mTheme = R.style.DefaultLightTheme;
             break;
          case DEFAULT_LIGH_DARK_THEME:
             MainActivity.mTheme = R.style.DefaultLightDarkTheme;
             break;
          case RED_THEME:
             MainActivity.mTheme = R.style.RedTheme;


5. 更改完mTheme的值之后，关闭当前Activity，并重新加载Activity。当重新加载Activity的时候，就会执行到Activity的onCreate方法，setTheme方法就会被调    用，修改的主题生效。
  ****注意：关闭Activity之前，要取消Activity的动画，开启Activity之前也一样，要取消掉动画，造成一种主题颜色直接被刷新的假象，提升用户体验。
  
  
        protected void reload() {
         // 获取开启这个Activity时的那个Intent
         Intent intent = getActivity().getIntent();
         // 取消关闭Activity时的默认动画
         getActivity().overridePendingTransition(0, 0);
         intent.addFlags(Intent.FLAG_ACTIVITY_NO_ANIMATION);
         // 关闭Activity
         getActivity().finish();
         // 取消开启Activity时的默认动画
         getActivity().overridePendingTransition(0, 0);
         // 重启Activity
         startActivity(intent);
      }

  细节处理：
   假如应用中存在大量的Activity，应抽取一个BaseActivity，在BaseActivity中的onCreate方法中设置主题，使所有的Activity的主题一起得到更改； 
   如果想更新过一次主题后，下次再进应用的时候，主题仍旧生效，需要每次更新主题后，将其标识存到SP中。


2.阴影处理

     <!-- 阴影的作用 -->
               <LinearLayout
                   android:layout_width="match_parent"
                   android:layout_height="wrap_content"
                   android:layout_margin="10dp"
                   android:orientation="vertical">

                   <TextView
                       android:layout_width="100dp"
                       android:layout_height="100dp"
                       android:layout_gravity="center_horizontal"
                               android:layout_marginTop="10dp"
                               android:background="@drawable/circle_shape"
                               android:elevation="5dp"
                               android:gravity="center"
                               android:text="高度5dp" />

                   <TextView
                       android:layout_width="100dp"
                       android:layout_height="100dp"
                       android:layout_gravity="center_horizontal"
                       android:layout_marginTop="-20dp"
                       android:background="@drawable/head"
                       android:elevation="10dp"
                       android:gravity="center"
                       android:text="高度10dp" />
               </LinearLayout>
               
       ### Android提供了一个outlineProvider属性，用于设置阴影的类型。它有四个属性值：none、background、bounds、paddedBounds
       
       在drawable文件夹下创建一个shape资源作为背景：shape_bg_circle.xml
       <?xml version="1.0" encoding="utf-8"?>
       <shape xmlns:android="http://schemas.android.com/apk/res/android"
               android:shape="oval">
               <!-- 边框 -->
               <stroke
                   android:width="1dip"
                   android:color="#eaff35" />
               <!-- 填充 -->
               <solid android:color="#eaff35" />
       </shape>

       以上面的shape资源作为TextView的背景，修改outlineProvider属性：
              <TextView
                  android:layout_width="100dp"
                  android:layout_height="100dp"
                  android:layout_margin="10dp"
                  android:background="@drawable/circle_shape"
                  android:elevation="10dp"
                  android:gravity="center"
                  android:outlineProvider="background"
                  android:text="background" />
                  
       none : 不设置阴影
       background：围绕着背景产生一圈阴影
       bounds：围绕着控件的边框产生一圈阴影
       ***注意一点：带有透明通道的背景，默认不显示阴影

自定义阴影 - setOutlineProvider
            /**
             *  自定义阴影
             */
            private void initShadow() {
                mTvShapeAlpha.setOutlineProvider(new ViewOutlineProvider() {
                    @Override
                    public void getOutline(View view, Outline outline) {
                        outline.setOval(0, 0, view.getWidth(), view.getHeight());
                    }
                });

                mTvImg.setOutlineProvider(new ViewOutlineProvider() {
                    @Override
                    public void getOutline(View view, Outline outline) {
                        outline.setOval(0, 0, view.getWidth(), view.getHeight());
                    }
                });
            }
            
            
            根据阴影裁剪控件 - setClipToOutline
            将控件的背景按阴影的样式进行裁剪，需要进行以下几步：
             1. 给控件设置OutlineProvider
             2. 将控件的setClipToOutline方法设置true
            注意：并不所有的形状都可以剪裁，可通过OutlineProvider.canClip()确认当前形状是否可以裁剪

            原始控件的布局设置：
                 <TextView
                   android:layout_width="100dp"
                   android:layout_height="100dp"
                   android:layout_margin="10dp"
                   android:background="#88FF0000"
                   android:elevation="5dp"
                   android:gravity="center"
                   android:text="原始图像" />


            根据阴影进行裁剪：
                                 /**
                                 *  阴影的裁剪
                                 */
                                private void clipShadow() {
                                    // 三角形的阴影
                                    ViewOutlineProvider triangleProvider = new ViewOutlineProvider() {
                                        @Override
                                        public void getOutline(View view, Outline outline) {
                                            Path path = new Path();
                                            path.moveTo(view.getWidth() / 2, 0);
                                            path.lineTo(0, view.getHeight());
                                            path.lineTo(view.getWidth(), view.getHeight());
                                            path.close();
                                            outline.setConvexPath(path);

                                            Log.e(TAG, "三角形阴影，是否可裁剪：" +outline.canClip());
                                        }
                                    };

                                    // 圆形的阴影
                                    ViewOutlineProvider ovalProvider = new ViewOutlineProvider() {
                                        @Override
                                        public void getOutline(View view, Outline outline) {
                                            outline.setOval(0, 0, view.getWidth(), view.getHeight());
                                            Log.e(TAG, "圆形阴影，是否可裁剪：" +outline.canClip());
                                       }
                                   };

                                   // 设置阴影
                                   mTvTriangle.setOutlineProvider(triangleProvider);
                                   mTvOval.setOutlineProvider(ovalProvider);

                                   // 裁剪控件
                                   mTvTriangle.setClipToOutline(true);
                                   mTvOval.setClipToOutline(true);


