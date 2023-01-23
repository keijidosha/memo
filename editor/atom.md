Atom

* Command + D で選択した単語を逆順に非選択にしていく  
`Command + U`
* auto indent にショートカットキーを割り当てる
  1. メニューから "Keymap" を選択して、設定ファイルを開く
  1. 次の行を追記
     ```
     'atom-text-editor':
         'ctrl-shift-i': 'editor:auto-indent'
     ```
  1. 保存する
* コメントの文字色が見づらいので変更する
  1. メニューから "Stylesheet..." を選択する。
  1. 表示されたエディタに次の行を追加する
     ```
     atom-text-editor.editor {
         .syntax--comment {
             color: #8eb;
         }
     }
     ```
  1. 保存する
