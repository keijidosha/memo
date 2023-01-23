# ant

* build.xml の設定値に改行文字を含める  
「&#xD;&#xA;」、「${line.separator}」を使う  
(例)  
`<replaceregexp file="hoge.txt" encoding="UTF-8" match="(xxx)" replace="\1${line.separator}abc&#xD;&#xA;123" />`

