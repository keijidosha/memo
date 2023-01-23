# concurrent

## 排他制御

### Lock, Semaphore, synchronized の使い分け

* java.util.concurrent.locks.Lock  
すでにロックがかかっている状態で、同じスレッドからさらにロックをかけてもブロックされない  
synchronized より細かな制御ができ、負荷が軽い(らしい)  
(出典) https://www.ibm.com/developerworks/jp/java/library/j-jtp10264/  
* synchronized  
動きは Lock と同じだが、ロックを解除し忘れる危険性がない(ブロックを抜けると必ずロック解除される)  
* java.util.concurrent.Semaphore  
ロックをかけたスレッドとは別のスレッドからでもロックを解除することができる  
反面、ロックをかけたスレッドからさらにロックをかけようとすると、誰かが(別のスレッドから)解除するまでブロックされる  

(参考) https://coderanch.com/t/615796/java/reason-prefer-binary-Semaphore-Reentrant

