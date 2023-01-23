# Android UI


#### タイトルバーを非表示にする
```
requestWindowFeature(Window.FEATURE_NO_TITLE);
```
この API は、画面に部品を配置する前、つまり「setContentView(int)」より前に実行しておきます。  
でないと「AndroidRuntimeException: requestFeature() must be called before adding content」がスローされます。

#### ダイアログ
メッセージ表示
```
AlertDialog.Builder dlg = new AlertDialog.Builder(this);
dlg.setIcon(R.drawable.icon);
dlg.setTitle("タイトル");
dlg.setMessage("メッセージ");
dlg.show();
```

#### ボタン1個表示
```
AlertDialog.Builder dlg = new AlertDialog.Builder(this);
dlg.setIcon(R.drawable.icon);
dlg.setTitle("タイトル");
dlg.setMessage("メッセージ");
dlg.setPositiveButton("了解", null);
dlg.show();
```

#### 二者択一
```
AlertDialog.Builder dlg = new AlertDialog.Builder(this);
dlg.setIcon(R.drawable.icon);
dlg.setTitle("タイトル");
dlg.setMessage("メッセージ");
dlg.setPositiveButton("はい", new DialogInterface.OnClickListener() {
    @Override
    public void onClick(DialogInterface dialog, int which) {
        // 「はい」が押された時の処理
    }
});
dlg.setNegativeButton("いいえ", null);
dlg.show();
```
