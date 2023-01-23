* 追加する電話番号を指定して連絡先アプリを起動  
  ```
    Intent intent = new Intent(Intent.ACTION_INSERT);
    intent.setType(ContactsContract.Contacts.CONTENT_TYPE);
    intent.putExtra(Insert.PHONE, "05055555555");
    startActivity(intent);
  ```
