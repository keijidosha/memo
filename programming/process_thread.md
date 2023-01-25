# プロセス、スレッド

## プロセス実行

### PHP

```
<?php
$proc = popen( "ls -l *.php *.txt 2>&1", "r" );
while( $line = fgets( $proc )) {
    echo $line;
}
pclose( $proc );
?>
```

proc_open() を使って、標準入力、標準出力、標準エラー、戻り値を扱う
```
<?php
$spec = array(
    0 => array( "pipe", "r" ),
    1 => array( "pipe", "w" ),
    2 => array( "pipe", "w" ),
);

$proc = proc_open( "ls -l *.php *.txt", $spec, $pipes );
if( is_resource( $proc )) {
    fclose( $pipes[0] );
    echo "stdout: " . stream_get_contents( $pipes[1] );
    fclose( $pipes[1] );
    echo "stderr: " . stream_get_contents( $pipes[2] );
    fclose( $pipes[2] );
    $ret = proc_close( $proc );
    echo "exit: " . $ret . "\n";
}
?>
```

### Ruby

```
require "open3"

stdout, stderr, status = Open3.capture3( "ls -l *.rb *.txt" )
puts stdout
puts stderr
puts status.exitstatus
```

### Java

```
import java.io.*;

public class Process2 {
    public Process2() {
        try {
            final Process proc = Runtime.getRuntime().exec(
                  new String[] { "ls", "-l", "Process2.java" , "*.txt" });
            final StringBuilder sbOut = new StringBuilder();
            final StringBuilder sbErr = new StringBuilder();
            Thread outThread = readStream( proc.getInputStream(), sbOut );
            Thread errThread = readStream( proc.getErrorStream(), sbErr );
            proc.waitFor();
            outThread.join();
            errThread.join();

            System.out.println( "stdout: " + sbOut );
            System.out.println( "stderr: " + sbErr );
            System.out.println( "exit code: " + proc.exitValue());

            proc.destroy();
        } catch( IOException ex ) {
            ex.printStackTrace();
        } catch( InterruptedException ex ) {
            ex.printStackTrace();
        }
    }

    private Thread readStream( final InputStream stream, final StringBuilder sb ) {
        Thread thread = new Thread() {
            @Override
            public void run() {
                try( Reader reader = new InputStreamReader( stream )) {
                    char[] buf = new char[1024];
                    int len;
                    while(( len = reader.read( buf )) >= 0 ) {
                        sb.append( buf, 0, len );
                    }
                } catch( IOException ex ) {
                    ex.printStackTrace();
                }
            }
        };
        thread.start();

        return thread;
    }

    public static void main( String[] args ) {
        new Process2();
    }
}
```

Java では Unix系OS で実行した場合も、コマンドライン引数の * はワイルドカード展開されない。  
ls -l "*.txt" のように、クォートで囲って実行したのと同じになる。  

ExecutorService と Callable を使うと
```
import java.io.*;
import java.util.concurrent.*;

public class Process4 {
    public Process4() {
        ExecutorService pool = Executors.newFixedThreadPool( 2 );
        try {
            final Process proc = Runtime.getRuntime().exec(
                  new String[] { "ls", "-l", "Process2.java" , "Process2.txt" });

            Future<String> futureOut = pool.submit( new ReadStream( proc.getInputStream()));
            Future<String> futureErr = pool.submit( new ReadStream( proc.getErrorStream()));

            proc.waitFor();

            System.out.println( "stdout: " + futureOut.get());
            System.out.println( "stderr: " + futureErr.get());
            System.out.println( "exit code: " + proc.exitValue());

            proc.destroy();
        } catch( IOException ex ) {
            ex.printStackTrace();
        } catch( ExecutionException ex ) {
            ex.printStackTrace();
        } catch( InterruptedException ex ) {
            ex.printStackTrace();
        } finally {
            pool.shutdownNow();
        }
    }

    public static void main( String[] args ) {
        new Process4();
    }

    private class ReadStream implements Callable<String> {
        private InputStream is = null;
        public ReadStream( InputStream is ) {
            this.is = is;
        }

        @Override
        public String call() throws IOException {
            StringBuilder sb = new StringBuilder();
            try( Reader reader = new InputStreamReader( is )) {
                char[] buf = new char[1024];
                int len;
                while(( len = reader.read( buf )) >= 0 ) {
                    sb.append( buf, 0, len );
                }
            }

            return sb.toString();
        }
    }
}
```

### Kotlin

```
import kotlin.concurrent.*

fun main( args: Array<String> ) {
    val proc = Runtime.getRuntime().exec( arrayOf( "ls", "-l", "Process3.kt" , "Process3.txt" ))
    val sbOut = StringBuilder()
    val sbErr = StringBuilder()
    val outThread = thread {
        proc.inputStream.reader().use() {
            sbOut.append( it.readText())
        }
    }
    val errThread = thread {
        proc.errorStream.reader().use() {
            sbErr.append( it.readText())
        }
    }

    proc.waitFor()
    outThread.join()
    errThread.join()

    println( "stdout: " + sbOut )
    println( "stderr: " + sbErr )
    println( "exit code: " + proc.exitValue())
    proc.destroy()
}
```

ProcessBuilder を使うことで、標準出力、エラー出力の出力先をファイルにすることができる。
```
fun main( args: Array<String> ) {
    val pb = ProcessBuilder( "ls", "-l", "Process3.kt" , "Process3.txt" )
    pb.redirectError( ProcessBuilder.Redirect.appendTo( File( "error.txt" )))
    pb.redirectOutput( ProcessBuilder.Redirect.appendTo( File( "output.txt" )))
    val proc = pb.start()
    proc.waitFor()

    println( "stdout: " + File( "output.txt" ).readText())
    println( "stderr: " + File( "error.txt" ).readText() )
    println( "exit code: " + proc.exitValue())
    proc.destroy()
}
```

さらにエラー出力を標準出力にリダイレクトしてファイル出力することも可。
```
fun main( args: Array<String> ) {
    val pb = ProcessBuilder( "ls", "-l", "Process3.kt" , "Process3.txt" )
    pb.redirectErrorStream( true )
    pb.redirectOutput( ProcessBuilder.Redirect.appendTo( File( "output.txt" )))
    val proc = pb.start()
    proc.waitFor()

    println( "stdout + stderr: " + File( "output.txt" ).readText())
    println( "exit code: " + proc.exitValue())
    proc.destroy()
}
```

