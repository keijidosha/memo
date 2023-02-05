
Spring Securiy 5.3 以前のコード例
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers( "/css/**", "/img/**", "/webjars/**" );
    }

    @Override
    protected void configure( HttpSecurity http ) throws Exception {
        http.authorizeRequests()
            .antMatchers( "/login" ).permitAll()
            .anyRequest().authenticated()
            .and()
        .formLogin()
            .loginPage("/login")
            .defaultSuccessUrl("/")
            .and()
        .logout()
            .logoutRequestMatcher( new AntPathRequestMatcher( "/logout" ))
            .and()
        .rememberMe();
    }

    @Override
    protected void configure( AuthenticationManagerBuilder auth ) throws Exception {
        auth.inMemoryAuthentication()
            .withUser( "admin" ).password( passwordEncoder().encode( "password" ))
            .authorities("ROLE_ADMIN")
            .and()
        .withUser( "user" )
            .password( passwordEncoder().encode( "password" ))
            .authorities( "ROLE_USER" );
    }
}
```
これを Spring security 6.0 用に書き換える。
```java
@Configuration
@EnableWebSecurity
// WebSecurityConfigurerAdapter を継承しない
public class SecurityConfig {
    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    // configure(WebSecurity web) をオーバーライドする代わりに HttpSecurity.authorizeHttpRequests() の戻り値を使う
    // configure( HttpSecurity http ) をオーバーライドする代わりに SecurityFilterChain を返す Bean を定義する
    @Bean
    public SecurityFilterChain securityFilterChain( HttpSecurity http )  {
        // authorizeRequests() の代わりに authorizeHttpRequests() を使う
        http.authorizeHttpRequests()
            // antMatchers() の代わりに requestMatchers() を使う
            .requestMatchers(
                "/css/**",
                "/img/**",
                "/webjars/**"
            // authenticated() の代わりに permitAll() を使う
            ).permitAll()
            .requestMatchers( "/login" ).permitAll()
            .anyRequest().authenticated()
            .and()
        .formLogin()
            .loginPage( "/login" )
            .defaultSuccessUrl( "/" )
            .and()
        .logout()
            .logoutRequestMatcher( AntPathRequestMatcher( "/logout" ))
            .and()
        .rememberMe();

        return http.build();
    }

    // configure( AuthenticationManagerBuilder auth ) をオーバーライドする代わりに UserDetailsService を返す Bean を定義する
    @Bean
    public UserDetailsService userDetailService() {
        InMemoryUserDetailsManager manager = InMemoryUserDetailsManager();
        manager.createUser(
            User.withUsername( "admin" )
                .password( passwordEncoder().encode( "password" ))
                // "ROLE_ADMIN の代わりに "ADMIN" を指定
                .authorities( "ADMIN" )
                .build()
        );
        manager.createUser(
            User.withUsername( "user" )
                .password( passwordEncoder().encode( "password" ))
                // "ROLE_USER の代わりに "USER" を指定
                .authorities( "USER" )
                .build()
        );

        return manager;
    }
}
```
UserDetailsService を返す Bean は次のように定義しても OK
```java
@Bean
public UserDetailsService userDetailsService() {
    UserDetails user = User
        .withUsername("user")
        .password("\$2a\$10\$GSVmY7rf0S8fmJ/xuLmZB.2V9i0VSTxU7nhWETl8j01gQwIKNKRkS")
        .authorities("USER")
        .build();
    UserDetails admin = User
        .withUsername("admin")
        .password("\$2a\$10\$lqrM4SVtS95fr8DY447eD.piM1wARsG8XgFWsecGfr6tZHPUUPqCe")
        .authorities("ADMIN")
        .build();
    return new InMemoryUserDetailsManager(user, admin);
}
```
