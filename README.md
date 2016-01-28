# ActivityCommWithFragment
解决activity与fragment之间互相通信的一种方案

参考博客：[Android：Activity与Fragment通信(99%)完美解决方案](http://www.jianshu.com/p/1b824e26105b)
### A. handle
### B. Broadcast
### C. EventBus
### D. interface

选择一种，并且说出为什么用这种，而且不用其他三种的原因。
--------------------------------------------------------------
如果没有答案，或者不确定选哪种好，那么下面的内容将给你来来一种99%完美解决Activity与Fragment通信的方案。

Android中activity与fragment的通讯相信大家用上面的4种方案都可以实现，但到底哪个更好呢

先让我们聊聊Fragment为什么出现，这对于我们解决Activity与Fragment的通信有帮助。一个新事物的产生总是为了解决旧事物存在的问题，Fragment是android3.0的产物，在android3.0之前解决手机、平板电脑的适配问题是很头疼的，对ActivityGroup有印象的朋友，应该能深深的体会到ActivityGroup包裹的多个Activity之间切换等一系列的性能问题。由此Fragment诞生了。个人总结的Fragment的使命：

解决手机、平板电脑等各种设备的适配问题
解决多个Activity之间切换性能问题
模块化，因为模块化导致复用的好处
## Fragment的使用
Fragment是可以被包裹在多个不同Activity内的，同时一个Activity内可以包裹多个Fragment，Activity就如一个大的容器，它可以管理多个Fragment。所有Activity与Fragment之间存在依赖关系。

## Activity与Fragment通信方案
上文提到Activity与Fragment之间是存在依赖关系的，因此它们之间必然会涉及到通信问题，解决通信问题必然会涉及到对象之间的引用。因为Fragment的出现有一个重要的使命就是：模块化，从而提高复用性。若达到此效果，Fragment必须做到高内聚，低耦合。

## handle方案
`public class MainActivity extends FragmentActivity{ 
      //申明一个Handler 
      public Handler mHandler = new Handler(){       
          @Override
           public void handleMessage(Message msg) { 
                super.handleMessage(msg);
                 ...相应的处理代码
           }
     }
     ...相应的处理代码
   }`

`public class MainFragment extends Fragment{ 
          //保存Activity传递的handler
           private Handler mHandler;
           @Override
           public void onAttach(Activity activity) { 
                super.onAttach(activity);
               //这个地方已经产生了耦合，若还有其他的activity，这个地方就得修改 
                if(activity instance MainActivity){ 
                      mHandler =  ((MainActivity)activity).mHandler; 
                }
           }
           ...相应的处理代码
     }`
该方案存在的缺点：
1、Fragment对具体的Activity存在耦合，不利于Fragment复用
2、不利于维护，若想删除相应的Activity，Fragment也得改动
3、没法获取Activity的返回数据
4、handler的使用个人感觉就很不爽（不知大家是否有同感)
## 广播方案：
1、用广播解决此问题有点大材小用了，个人感觉广播的意图是用在一对多，接收广播者是未知的情况
2、广播性能肯定会差（不要和我说性能不是问题，对于手机来说性能是大问题）
3、传播数据有限制（必须得实现序列化接口才可以）
4、暂时就想到这些缺点，其他的缺点请大家集思广益下吧。
## EventBus方案：
1、EventBus是用反射机制实现的，性能上会有问题（不要和我说性能不是问题，对于手机来说性能是大问题）
2、EventBus难于维护代码
3、没法获取Activity的返回数据
## 接口方案：
`//MainActivity实现MainFragment开放的接口 
  public class MainActivity extends FragmentActivity implements FragmentListener{ 
        @override
         public void toH5Page(){ }
       ...其他处理代码省略
   }` 
  `public class MainFragment extends Fragment{

         public FragmentListener mListener;  
        //MainFragment开放的接口 
        public static interface FragmentListener{ 
            //跳到h5页面
           void toH5Page();
         }

         @Override 
        public void onAttach(Activity activity) { 
              super.onAttach(activity); 
              //对传递进来的Activity进行接口转换
               if(activity instance FragmentListener){
                   mListener = ((FragmentListener)activity); 
              }
         }
         ...其他处理代码省略 
  }`
这种方案应该是既能达到复用，又能达到很好的可维护性，并且性能也是杠杠的。但是唯一的一个遗憾是假如项目很大了，Activity与Fragment的数量也会增加，这时候为每对Activity与Fragment交互定义交互接口就是一个很头疼的问题（包括为接口的命名，新定义的接口相应的Activity还得实现，相应的Fragment还得进行强制转换）。 想看更好的解决方案请阅读原文。
原文链接：[Android：Activity与Fragment通信(99%)完美解决方案](http://www.jianshu.com/p/1b824e26105b)
