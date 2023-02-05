
Spring Securiy 5.3 以前のコード例
```
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
```
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public SecurityFilterChain securityFilterChain( HttpSecurity http )  {
        http.authorizeHttpRequests()
            .requestMatchers(
                "/css/**",
                "/img/**",
                "/webjars/**"
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

    @Bean
    public UserDetailsService userDetailService() {
        InMemoryUserDetailsManager manager = InMemoryUserDetailsManager();
        manager.createUser(
            User.withUsername( "admin" )
                .password( passwordEncoder().encode( "password" ))
                .authorities( "ADMIN" )
                .build()
        );
        manager.createUser(
            User.withUsername( "user" )
                .password( passwordEncoder().encode( "password" ))
                .authorities( "USER" )
                .build()
        );

        return manager;
    }
}
```


