# UI

* UINavigationViewController を使った view の切替え時にNavigationBar や ToolBar の表示/非表示を設定する  
(表示)
`setNavigationBarHidden:(Boolean) animated:(Boolean)`  
(非表示)  
`setToolBarHidden:(Boolean) animated:(Boolean)`
* iOS7 で表示が上にずれる
  ```
  - (void)viewDidLoad
  {
      [super viewDidLoad];
  
      if (floor(NSFoundationVersionNumber) > NSFoundationVersionNumber_iOS_6_1) {
          NSArray* sv = self.view.subviews;
          for( UIView* v in sv ) {
                    v.frame = CGRectMake(v.frame.origin.x, v.frame.origin.y + 20, v.frame.size.width, v.frame.size.height);
                }
      }
  }
  ```
