- Table of Content  
{:toc}


# 正規表現

## 先読み・後読み

* 肯定的先読み(直後に def がある)  
abc(?=def)
* 否定的先読み(直後に def がない)  
abc(?!def)
* 肯定的後読み(直前に def がある)  
(?<=def)abc
* 否定的後読み(直前に def がない)  
(?<!def)abc
