# snippet

## background

* バックグラウンドに入った後も 10分間処理を継続
  ```
  void (^handler)(void) = ^{
      UIApplication* app = [UIApplication sharedApplication]; [app endBackgroundTask:_bgTask]; 
      _bgTask = UIBackgroundTaskInvalid; 
  }; 
  
  _bgTask = [[UIApplication sharedApplication] beginBackgroundTaskWithExpirationHandler:handler];
  ```
* バックグラウンドに入った後、10分間に 1回 iOS から呼び出してもらって処理を実行
  ```
  (VoIPアプリのみ)
  [[UIApplication sharedApplication] setKeepAliveTimeout:600.0 handler:^{
   // ここに実行したい処理を記述
  }];
  ```

## システム情報取得

* OSバージョン取得  
`[[UIDevice currentDevice]systemVersion]`
* OS名取得  
`[[UIDevice currentDevice]systemName]`
* 機種名取得  
`[[UIDevice currentDevice]model]`
* キャリア名取得
  ```
  CTTelephonyNetworkInfo* networkInfo = [[CTTelephonyNetworkInfo alloc] init];
  CTCarrier* carrier = [networkInfo subscriberCellularProvider];
  NSString* carrierName = carrier.carrierName;
  ```

## ダイアログ表示

```
// delegate パラメーターには nil をセットしてもよい
UIAlertView *alert = [[UIAlertView alloc]initWithTitle:@"タイトル" message:@"メッセージ" delegate:self cancelButtonTitle:@"了解" otherButtonTitles:nil];
[alert show];

// ボタンが押された時のデレゲート
-(void)alertView:(UIAlertView*)alertView
        clickedButtonAtIndex:(NSInteger)buttonIndex
{

  switch (buttonIndex) {
    case 0:
      break;
    case 1:
      break;
  }

}

// アラート表示直前のデレゲート
-(void)willPresentAlertView:(UIAlertView*)alertView
{
}

// アラート表示直後のデレゲート
-(void)didPresentAlertView:(UIAlertView*)alertView
{
}
```

## 数値

* 整数を 3桁で桁区切りして書式化
  ```
  NSNumberFormatter* numFmt = [[NSNumberFormatter alloc] init];
  [numFmt setNumberStyle:NSNumberFormatterDecimalStyle];
  [numFmt setGroupingSeparator:@","];
  [numFmt setGroupingSize:3];
  [numFmt stringFromNumber:[NSNumber numberWithInt:1234567]]];
  ```

## 日付、時刻

* 年、月、日を取り出す
  ```
  NSCalendar* calendar = [NSCalendar currentCalendar];
  NSUInteger flag = NSYearCalendarUnit | NSMonthCalendarUnit | NSDayCalendarUnit;
  NSDate* now = [NSDate date];
  NSDateComponents* compo = [_calendar components:flag fromDate:now];
  NSInteger year = compo.year;
  NSInteger month = compo.month;
  NSInteger day = compo.day;
  ```
* 時、分、秒を取り出す
  ```
  NSCalendar* calendar = [NSCalendar currentCalendar];
  NSUInteger flag = NSHourCalendarUnit | NSMinuteCalendarUnit | NSSecondCalendarUnit;
  NSDate* now = [NSDate date];
  NSDateComponents* compo = [_calendar components:flag fromDate:now];
  NSInteger hour = compo.hour;
  NSInteger minute = compo.minute;
  NSInteger second = compo.second;
  ```
* 年月日時分秒ミリ秒を文字列に書式化する
  ```
  NSDateFormatter* dateFmt = [[NSDateFormatter alloc] init];
  [dateFmt setDateFormat:@"yyyy-MM-dd HH:mm:ss.SSSS"];
  NSDate* now = [NSDate date];
  NSString* dateString = [dateFmt stringFromDate:now];
  ```

## 空きメモリー取得

```
#import <mach/mach.h>
#import <mach/mach_host.h>

+(natural_t) get_free_memory {
    mach_port_t host_port;
    mach_msg_type_number_t host_size;
    vm_size_t pagesize;
    host_port = mach_host_self();
    host_size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    host_page_size(host_port, &pagesize);
    vm_statistics_data_t vm_stat;

    if (host_statistics(host_port, HOST_VM_INFO, (host_info_t)&vm_stat, &host_size) != KERN_SUCCESS) {
        NSLog(@"Failed to fetch vm statistics");
        return 0;
    }

    /* Stats in bytes */
    natural_t mem_free = vm_stat.free_count * pagesize;
    return mem_free;
}
```

http://stackoverflow.com/questions/4579642/monitor-memory-usage-in-an-iphone-app
