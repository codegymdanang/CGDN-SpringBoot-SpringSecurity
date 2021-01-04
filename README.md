# CGDN-SpringBoot-SpringSecurity
## Chuan bi database gom 3 bang
-- Create table
create table APP_USER
(
  USER_ID           BIGINT not null,
  USER_NAME         VARCHAR(36) not null,
  ENCRYTED_PASSWORD VARCHAR(128) not null,
  ENABLED           BIT not null
) ;
--
alter table APP_USER
  add constraint APP_USER_PK primary key (USER_ID);

alter table APP_USER
  add constraint APP_USER_UK unique (USER_NAME);


-- Create table
create table APP_ROLE
(
  ROLE_ID   BIGINT not null,
  ROLE_NAME VARCHAR(30) not null
) ;
--
alter table APP_ROLE
  add constraint APP_ROLE_PK primary key (ROLE_ID);

alter table APP_ROLE
  add constraint APP_ROLE_UK unique (ROLE_NAME);


-- Create table
create table USER_ROLE
(
  ID      BIGINT not null,
  USER_ID BIGINT not null,
  ROLE_ID BIGINT not null
);
--
alter table USER_ROLE
  add constraint USER_ROLE_PK primary key (ID);

alter table USER_ROLE
  add constraint USER_ROLE_UK unique (USER_ID, ROLE_ID);

alter table USER_ROLE
  add constraint USER_ROLE_FK1 foreign key (USER_ID)
  references APP_USER (USER_ID);

alter table USER_ROLE
  add constraint USER_ROLE_FK2 foreign key (ROLE_ID)
  references APP_ROLE (ROLE_ID);


-- Used by Spring Remember Me API.
CREATE TABLE Persistent_Logins (

    username varchar(64) not null,
    series varchar(64) not null,
    token varchar(64) not null,
    last_used timestamp not null,
    PRIMARY KEY (series)

);

## Them du lieu vao db user va admin vao db
insert into App_User (USER_ID, USER_NAME, ENCRYTED_PASSWORD, ENABLED)
values (2, 'dbuser1', '$2a$10$PrI5Gk9L.tSZiW9FXhTS8O8Mz9E97k2FZbFvGFFaSsiTUIl.TCrFu', 1);

insert into App_User (USER_ID, USER_NAME, ENCRYTED_PASSWORD, ENABLED)
values (1, 'dbadmin1', '$2a$10$PrI5Gk9L.tSZiW9FXhTS8O8Mz9E97k2FZbFvGFFaSsiTUIl.TCrFu', 1);

---

insert into app_role (ROLE_ID, ROLE_NAME)
values (1, 'ROLE_ADMIN');

insert into app_role (ROLE_ID, ROLE_NAME)
values (2, 'ROLE_USER');

---

insert into user_role (ID, USER_ID, ROLE_ID)
values (1, 1, 1);

insert into user_role (ID, USER_ID, ROLE_ID)
values (2, 1, 2);

insert into user_role (ID, USER_ID, ROLE_ID)
values (3, 2, 2);

## Neu nhu cac em mong muon kiem tra xem password co dung khong thi minh phai viet 1 class rieng va ke thua lai class AuthenticationProvider  nhu sau. Trong phuong thuc override authentication thi minh kiem tra password co dung khong

public class MyClass implements AuthenticationProvider {

public Authentication authenticate(Authentication authentication) throws AuthenticationException {

		
		if (authentication.getPrincipal() == null || authentication.getCredentials() == null) {
			throw new BadCredentialsException("Username or Password empty");
		}

		
		String username = authentication.getName();
        String password = authentication.getCredentials().toString();

		UserDetails userDetails = userDetailsService.loadUserByUsername(username);

		if (userDetails == null) {
			logger.info("User {} not existed", username);
			throw new BadCredentialsException("User not found");
		}

		// Authenticate
		String passwordHash = CommonUtils.md5(password);
		if(!passwordHash.equals(userDetails.getPassword())){
			logger.info("User {} has enter an invalid password", username);
			throw new BadCredentialsException("Invalid password");
			
		}

		UsernamePasswordAuthenticationToken result = new UsernamePasswordAuthenticationToken(userDetails,
				authentication.getCredentials(), userDetails.getAuthorities());
		
		logger.info("Leaving AirtimeAuthenticationProvider.authenticate()");

		return result;
	}public Authentication authenticate(Authentication authentication) throws AuthenticationException {

		logger.info("Enteriing AirtimeAuthenticationProvider.authenticate()");
		
		if (authentication.getPrincipal() == null || authentication.getCredentials() == null) {
			throw new BadCredentialsException("Username or Password empty");
		}

		
		String username = authentication.getName();
        String password = authentication.getCredentials().toString();

		UserDetails userDetails = userDetailsService.loadUserByUsername(username);

		if (userDetails == null) {
			logger.info("User {} not existed", username);
			throw new BadCredentialsException("User not found");
		}

		// Authenticate
		String passwordHash = CommonUtils.md5(password);
		if(!passwordHash.equals(userDetails.getPassword())){
			logger.info("User {} has enter an invalid password", username);
			throw new BadCredentialsException("Invalid password");
			
		}

		UsernamePasswordAuthenticationToken result = new UsernamePasswordAuthenticationToken(userDetails,
				authentication.getCredentials(), userDetails.getAuthorities());
		
		logger.info("Leaving AirtimeAuthenticationProvider.authenticate()");

		return result;
	}
  }
