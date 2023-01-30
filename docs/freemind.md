# FreeMind

## Mojave JDK 1.8 で動くようにする

1. /usr/libexec/java_home -V  
   ```
   Matching Java Virtual Machines (3):
    11.0.3, x86_64:	"AdoptOpenJDK 11"	/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home
    11.0.2, x86_64:	"OpenJDK 11.0.2"	/Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home
    1.8.0_201, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home

   /Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home
   ```
1. cd /Applications/FreeMind.app/Contents/PlugIns/  
1. ln -s /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk .  
1. vim /Applications/FreeMind.app/Contents/Info.plist  
   ```
   <key>JVMRuntime</key>
   <string>jdk1.8.0_201.jdk</string>
   ```

(参考) https://freemind.asia/faqs/mac/sierra.html

## UTF-8 で保存するためのパッチ

16進数化への変換処理をコメントアウト
```
--- freemind/main/XMLElement.java.org	2014-04-13 03:05:35.000000000 +0900
+++ freemind/main/XMLElement.java	2019-07-05 19:17:31.000000000 +0900
@@ -2454,16 +2454,16 @@
 				writer.write(';');
 				break;
 			default:
-				int unicode = (int) ch;
-				if ((unicode < 32) || (unicode > 126)) {
-					writer.write('&');
-					writer.write('#');
-					writer.write('x');
-					writer.write(Integer.toString(unicode, 16));
-					writer.write(';');
-				} else {
+				// int unicode = (int) ch;
+				// if ((unicode < 32) || (unicode > 126)) {
+				// 	writer.write('&');
+				// 	writer.write('#');
+				// 	writer.write('x');
+				// 	writer.write(Integer.toString(unicode, 16));
+				// 	writer.write(';');
+				// } else {
 					writer.write(ch);
-				}
+				// }
 			}
 		}
 	}
```

ファイル出力エンコーディングに UTF-8 を指定
```
--- freemind/modes/mindmapmode/MindMapMapModel.java.org	2014-04-13 03:05:35.000000000 +0900
+++ freemind/modes/mindmapmode/MindMapMapModel.java	2019-07-05 19:16:55.000000000 +0900
@@ -283,7 +283,7 @@
 				timerForAutomaticSaving.cancel();
 			}
 			BufferedWriter fileout = new BufferedWriter(new OutputStreamWriter(
-					new FileOutputStream(file)));
+					new FileOutputStream(file), "UTF-8"));
 			getXml(fileout);
 
 			if (!isInternal) {
```
