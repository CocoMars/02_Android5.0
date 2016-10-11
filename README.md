# 02_Android5.0
 Android
 分别为主题编辑器 动态切换主题    
 1. 首先，先在styles.xml文件夹下定义一堆样式
 2. 在Activity中定义一个成员变量，来标识当前默认显示的主题：
3. 在Activity的onCreate方法中通过系统的“setTheme()”方法来设置当前Activity要显示的主题。
注意：必须在“super.onCreate()” 方法执行之前调用“setTheme()”方法：
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
