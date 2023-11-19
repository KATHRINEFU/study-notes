# Flutter studybook

## Getting started: Install and setup

Install: 

1. open terminal using rosetta

   install rosetta: code below

   finder - Applications - terminal - get info - open in rosetta

   restart

   run 'arch', get output 'i386'

   ```
   sudo softwareupdate --install-rosetta --agree-to-license
   ```

2. run 'flutter doctor'

3. resolve issue - sdk command line tools: 
   1. open androidstudio - preference - android sdk - sdk tools - commandline tools - download
   2. add tool to path: export PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin
   3. re-run 'flutter doctor', sign agreement, bingo!

4. run test app:
   1. run 'flutter create test_app'
   2. run 'cd test_app'
   3. open Simulator
   4. run 'run test_app'

5. setup editor - VSCode

   1. install 'Flutter' extension
   2. resolve issue - cannot find path to flutter sdk
      1. open terminal
      2. run 'sudo nano /etc/paths'
      3. add 'Users/KateFu/FlutterProjects/bin' to bottom
      4. Restart vscode

   3. test with F1 - flutter: run doctor

6. build a project
   1. f1 flutter: new project - application - select folder
   2. replace corresponding code
   3. Main.dart - start debugging

## Useful tips

1. extract widget: select - refactor - extract widget - assign name: create class automatically

2. wrap with padding: select - refactor - wrap with padding: return padding(...)

3. wrap with widget: select padding -refactor - wrape with padding - assign name: specify a parent widget

4. use consistent theme: 

   ```dart
    var theme = Theme.of(context);
    return Card(
         color: theme.colorScheme.primary, 
    )
   ```

   change theme for the whole app:

   scrolling up to `MyApp` and changing the seed color for the `ColorScheme` there.

5. change text style:

   `copyWith()` lets you change a lot more about the text style than just the color. 

   ```dart
   var style = theme.textTheme.displayMedium!.copyWith(
         color: theme.colorScheme.onPrimary,
       );
   
   return Card(
         color: theme.colorScheme.primary,
         child: Padding(
           padding: const EdgeInsets.all(20),
           child: Text(pair.asLowerCase, style: style),
         ),
       );
   ```

6. view source code: cmd+shift+click

7. center element:

   or select - refactor - wrap with center

   ```dart
   mainAxisAlignment: MainAxisAlignment.center
   ```

8. add spaces between widgets

   add a `SizedBox(height: 10)` widget

9. can use widget inspector to moniter

10. create a new collection (list/set) with type: var favorites = <WordPair>[];

11. Wrap in row: select- refactor - wrap in row

    mainAxisSize: MainAxisSize.min (tells `Row` not to take all available horizontal space.)

12. use the `ElevatedButton.icon()` constructor to create a button with an icon

    ```dart
    IconData icon;
        if (appState.favorites.contains(pair)) {
          icon = Icons.favorite;
        } else {
          icon = Icons.favorite_border;
        }
    
    ElevatedButton.icon(
                      onPressed: () {
                        appState.toggleFavorite();
                      },
                      icon: Icon(icon),
                      label: Text('Like'),
                  ),
    ```

13. navigation rail:  StatelessWidget

    Stateless widgets don't contain any mutable state of their own. None of the widgets can change *itself*—they must go through `MyAppState`

    ```dart
    // switch between the two screens
    class MyHomePage extends StatelessWidget {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: Row(
            children: [
              // The SafeArea ensures that its child is not obscured by a hardware notch or a status bar. In this app, the widget wraps around NavigationRail to prevent the navigation buttons from being obscured by a mobile status bar, for example.
              SafeArea(
                child: NavigationRail(
                  extended: false,
                  destinations: [
                    NavigationRailDestination(
                      icon: Icon(Icons.home),
                      label: Text('Home'),
                    ),
                    NavigationRailDestination(
                      icon: Icon(Icons.favorite),
                      label: Text('Favorites'),
                    ),
                  ],
                  selectedIndex: 0,
                  onDestinationSelected: (value) {
                    print('selected: $value');
                  },
                ),
              ),
              // Expanded widgets are extremely useful in rows and columns—they let you express layouts where some children take only as much space as they need (NavigationRail, in this case) and other widgets should take as much of the remaining room as possible (Expanded, in this case).
              Expanded(
                child: Container(
                  color: Theme.of(context).colorScheme.primaryContainer,
                  child: GeneratorPage(),
                ),
              ),
            ],
          ),
        );
      }
    }
    
    
    ```

14. StatefulWidget

    Select - refactor - convert to stateful widget: create a `_MyHomePageState`. This class extends `State`, and can therefore manage its own values.

    build function has been moved to _MyHomePageState class

    The underscore (`_`) at the start of `_MyHomePageState` makes that class private and is enforced by the compiler.

    ```dart
    //new
    class MyHomePage extends StatefulWidget {
      @override
      State<MyHomePage> createState() => _MyHomePageState();
    }
    
    //new
    class _MyHomePageState extends State<MyHomePage> {
      //new
      var selectedIndex = 0;
      @override
      Widget build(BuildContext context) {
        Widget page;
        switch (selectedIndex) {
          case 0:
            page = GeneratorPage();
            break;
          case 1:
            // if there's no page yet, can put a Placeholder, that draws a crossed rectangle wherever you place it, marking that part of the UI as unfinished.
            page = Placeholder();
            break;
          default:
            throw UnimplementedError('no widget for $selectedIndex');
        }
    		//old
        return Scaffold(
          body: Row(
            children: [
              SafeArea(
                child: NavigationRail(
                  extended: true,
                  destinations: [
                    NavigationRailDestination(
                      icon: Icon(Icons.home),
                      label: Text('Home'),
                    ),
                    NavigationRailDestination(
                      icon: Icon(Icons.favorite),
                      label: Text('Favorites'),
                    ),
                  ],
                  selectedIndex: selectedIndex,
                  onDestinationSelected: (value) {
                    // new
                    setState(() {
                      selectedIndex = value;
                    });
                  },
                ),
              ),
              Expanded(
                child: Container(
                  color: Theme.of(context).colorScheme.primaryContainer,
                  //new
                  child: page,
                ),
              ),
            ],
          ),
        );
      }
    }
    ```

15. auto respondsive widget

    `Wrap` is a widget similar to `Row` or `Column` that automatically wraps children to the next "line" (called "run") when there isn't enough vertical or horizontal space. There's `FittedBox`, a widget that automatically fits its child into available space according to your specifications.

16. Adapt to resize - LayoutBuilder

    lets you change your widget tree depending on how much available space you have.

    select returned Scaffold - refactor - wrap with builder - change type to LayoutBuilder - add builder parameter 'constraints'

    `LayoutBuilder`'s `builder` callback is called every time the constraints change. This happens when, for example:

    - The user resizes the app's window
    - The user rotates their phone from portrait mode to landscape mode, or back
    - Some widget next to `MyHomePage` grows in size, making `MyHomePage`'s constraints smaller
    - And so on

## Widgets



### MaterialApp (stateful)

Flutter整体结构图

![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/20191129195736853.png)

`MaterialApp` 包含了许多的 `Widget` ，这些 `Widget` 通常是实现 [Material Design](https://material.io/design) 的应用程序所必须要的，包含的 `Widget` 可以在 [Material Components widgets](https://flutter.dev/docs/development/ui/widgets/material) 中查看所有

1. navigatorKey

   实现无 `context` 跳转，那么可以通过设置该key, 通过 `navigatorKey.currentState.overlay.context` 获取全局context

2. scaffoldMessengerKey

   管理后代的 `Scaffolds`，可以实现无 `context` 调用 `snack bars`

   ```dart
   GlobalKey<ScaffoldMessengerState> _scaffoldKey = GlobalKey();
   
   MaterialApp(
     scaffoldMessengerKey: _scaffoldKey,
   );
   
   _scaffoldKey.currentState.showSnackBar(SnackBar(content: Text("show SnackBar")));
   
   ```

3. Home: 程序进入后的第一个界面，传入一个 `Widget`

   ```dart
   MaterialApp(
     home: Scaffold(...),
   );
   ```

4. routes: 生成路由表，以键值对形式传入 `key` 为路由名字， `value` 为对应的`Widget`

   ```dart
   MaterialApp(
     routes: {
       "/home": (_) => Home(),
       "/my": (_) => My()
       //....
     },
     initialRoute: "/home",
   );
   ```

   需要配合InitialRoute使用：初始路由，如果设置了该参数并且在 `routes` 找到了对应的key，将会展示对应的 `Widget` ，否则展示 `home`

5. onGenerateRoutes

   当跳转路由时，如果在routes中找不到对应的key，会执行该回调，返回一个RouteSettings，该对象有name和arguments

   ```dart
    MaterialApp(
      routes: {
        "/home": (_) => Home(),
        "/my": (_) => My()
        },
      initialRoute: "/home",
      onGenerateRoute: (setting) {
        // 这里可以做进一步的逻辑处理
        return MaterialPageRoute(builder: (_) => Home());
      },
   )
   ```

6. navigatorObservers

   路由监听器，主要是就是监听页面路由堆栈的变化，当页面进行 `push` `pop` `remove` `replace` 等操作时会进行监听。常用于ABtest

   ```dart
   MaterialApp(
    	navigatorObservers: [
       MyObserver()
     ],
   )
     
    class MyObserver extends NavigatorObserver {
     @override
     void didPush(Route route, Route previousRoute) {
       print(route);
       print(previousRoute);
       super.didPush(route, previousRoute);
     }
   }
   ```

7. builder

   当构建 `Widget` 前调用，主要用于字体大小、主题颜色等配置

   ```dart
   MaterialApp(
    	 builder: (_, child) {
        return Scaffold(appBar: AppBar(title: Text("build")), body: child,);
      },
   )
   ```

8. title, onGenerateTitle, color: 应用程序切换时效果

9. theme

   如果指定了 `darkTheme` ，那么用于提供用户界面的深色版本。如果提供了 `darkTheme` ， `themeMode` 将控制将使用哪个主题。默认值是 `ThemeData.light()` 应用程序的主题颜色

   ```dart
   MaterialApp(
     theme: ThemeData(
       // 主要颜色
       primaryColor: Colors.red
     ),
     darktheme: ThemeData(
       // 主要颜色
       primaryColor: Colors.green
     ),
     highContrastTheme: ThemeData(
       primaryColor: Colors.pink
     ),
     themeMode: ThemeMode.system
   )
     
   
   ```

10. locale, localizationDelegates, supportedLocales,localeListResolutionCallback

    主要用于语言切换时，如果为 `null` 时使用系统区域

    ```dart
    MaterialApp(
      locale: Locale('zh', 'CN') // 中文简体
    )
      
    MaterialApp(
      locale: Locale('zh', 'CN') // 中文简体
      localizationsDelegates: [
        GlobalMaterialLocalizations.delegate,
        GlobalWidgetsLocalizations.delegate,
      ],
      supportedLocales: [
        Locale('en', 'US'), //美国英语
        Locale("zh", 'CN'), //中文简体
      ],
      localeListResolutionCallback: (List<Locale> locales, Iterable<Locale> supportedLocales) {
        // 系统切换语言时调用
        return Locale("zh", 'CN');
      },
    )
    ```

### Scaffold

`Scaffold` 实现了基本的 [Material Design](https://material.io/design) 布局结构

1. appBar: 显示脚手架的顶部导航栏

2. body

   ```dart
   Scaffold(
     appBar: AppBar(
       title: Text("Scaffold"),
     ),
     body: Center(
     	child: Text("body"),
     ),
   );
   ```

3. floatingActionButton: 悬浮按钮，默认位于右小角

   ```dart
   bool _isOpen = false;
   late AnimationController _animationController;
   late Animation<Color?> _animationColor;
   late Animation<double> _animationIcon;
   Curve _curve = Curves.easeOut;
   
   @override
     void initState() {
       // TODO: implement initState
       super.initState();
   
       _animationController = AnimationController(vsync: this, duration: Duration(milliseconds: 250))
       ..addListener(() {
         setState(() {
   
         });
       });
   
       _animationColor = ColorTween(
         begin: Colors.red,
         end: Colors.green
       ).animate(CurvedAnimation(parent: _animationController, curve: Interval(0.0, 1.0, curve: _curve)));
   
       _animationIcon = Tween(begin: 0.0, end: 1.0).animate(_animationController);
     }
   
     @override
     void dispose() {
       // TODO: implement dispose
       _animationController.dispose();
       super.dispose();
     }
     
     floatingActionButton: FloatingActionButton(
           backgroundColor: _animationColor.value,
           onPressed: (){
             if (!_isOpen) {
               _animationController.forward();
             } else {
               _animationController.reverse();
             }
             _isOpen = !_isOpen;
           },
           child: AnimatedIcon(
             icon: AnimatedIcons.menu_close,
             progress: _animationIcon,
           )
         ),
      floatingActionButtonLocation: FloatingActionButtonLocation.endFloat,
      floatingActionButtonAnimator: FloatingActionButtonAnimator.scaling,
   ```

4. 