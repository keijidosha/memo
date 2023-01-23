# iOS7対応

## ボタンを非表示に設定してもなぜか表示されたままになってしまう

* 次のコードを実行しても、ボタンが非表示になりません。  
`[someButton setHidden:true];`
* そこで非表示にする前に UIViewController を参照するとうまくいきました。(原因は不明)
  ```
  if ( floor(NSFoundationVersionNumber) > NSFoundationVersionNumber_iOS_6_1) {
      (void)self.view;
  }
  [someButton setHidden:true];
  ```

## ダイアログのボタンがタップされたらタブ切替えを行う処理後に、iOS7 ではタブ切替え操作ができなくなる。

* ダイアログを表示し、ボタンがタップされたらタブをプログラムから切り替えていました。
  ```
  UIAlertView *alert = [[UIAlertView alloc]initWithTitle:@"タイトル" message:@"メッセージ" delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil];
  [alert show];
  - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
  {
      if( buttonIndex == 0 ) {
          self.navigationController.tabBarController.selectedIndex = 0;
      }
  }
  ```
  すると、それ以降タブをタップしても切り替わらなくなりました。
  * 回避策  
タブ切替え処理を別スレッドで実行するようにし、一瞬スリープしてから行うようにしました。
    ```
    UIAlertView *alert = [[UIAlertView alloc]initWithTitle:@"タイトル" message:@"メッセージ" delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil];
    [alert show];
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
    {
        if( buttonIndex == 0 ) {
            dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0), ^{
                [NSThread sleepForTimeInterval:0.001]; 
                [self performSelectorOnMainThread:@selector(tabChange) withObject:nil waitUntilDone:NO]; }); 
            });
        }
    }
    
    - (void)tabChange
    {
        self.navigationController.tabBarController.selectedIndex = 0;
    }
    ```

※Xcode 4.6.3 でビルドした場合はこの現象は発生しませんでした。
