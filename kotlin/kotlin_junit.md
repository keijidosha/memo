# Kotlin Junit

サンプル

```
import org.junit.Test
import org.junit.Before
import org.junit.BeforeClass
import org.junit.After
import org.junit.AfterClass
import org.junit.Assert.assertEquals
import org.junit.Assert.assertNotEquals
import org.junit.Assert.assertTrue
import org.junit.Assert.assertFalse
import org.junit.Assert.fail

public class SampleTest {
  companion object {
    @JvmStatic
    @BeforeClass
    fun beforClass() {
      // ...
    }

    @JvmStatic
    @AfterClass
    fun afterClass() {
      // ...
    }
   }

   @Before
   fun setUp() {
     // ...
   }

   @After
   fun tearDown() {
     // ...
   }


  @Test
  fun test1() {
    try {
      assertEquals( "hoge", "hoge" )
      assertNotEquals( "hoge", "fuga" )
      assertTrue( 2 > 1 )
      assertFalse( 2 < 1 )
      if( 1 < 2 ) throw Exception("hello")
        fail("fuga")
      } catch( ex: Exception ) {
      println( ex.message )
    }
  }
}
```
