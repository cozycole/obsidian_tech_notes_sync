# System Databases

SQL Server has the following system dbs:
- master
- msdb
- model
- Resource
- tempdb

## Viewing/Modifying System Data

SQL Server doesn't support directly updating the info of system tables, system stored procedures and catalog views. 
It instead provides:
- Admin utilities in SSMS
- SQL-SMO API for adminstering SQL Server in their applications
- T-SQL scripts and stored procedures that used system stored procedures

This is done to shield applications from internal workings of SQL Server. Since the function of SQL Server relies on these system tables,
if a table is changed in an update, the application query will have to be changed as well when updating the server. 


# dbo vs db_owner

dbo is a user and db_owner is a database role

# Login vs User

Logins give the ability to connect to a SQL Server instance, whereas a db user provides the login rights to access a database.

A "Login" grants the principal entry into the SERVER. There are two types:
- SQL Server authentication
- Windows authentication (mapped to either Windows users or groups)

These logins allow access to the server realm. Users are mapped to logins based on the if the SID property of logins and users match. 

A "User" grants a login entry into a single DATABASE. 

All work is done in the context of some database, and to get to do the work, one needs to first have access to the server through a login. Users need to be mapped to a login otherwise they are *orphaned users*.

This allows databases to be detached from a server and added to a new server, but users within that database must be remapped to logins in the new instance (using sp_change_users_login or ALTER USER statement)

Permissions can be granted at both the server level to logins (taking effect regardless of the db) and permissions at the db level to users.

# Principals

To help with the management of permissions, other entities (besides logins and users) can be used as grantees of a permission. Collectively, in SQL Server, the entities that can be granted a permission are referred to as *principals*. Principals can be either *server principals* or *database principals*.

NOTE: Logins grant access to the server, playing a role in the authentication process. The other server principals are only used for permission management and don't help with server authentication.

Examples of server principals besides logins include *server roles* and *logins mapped to certificate or asymmetric keys*.

Examples of database principals include *database roles* (fixed and flexible), *application roles*, *users mapped to certificate or assymetric keys*, and *loginless users*. All of these help with the assignment of the permissions.

# Execution Contexts (Server Execution Context and Database Execution Context)

When executing a query, SQL keeps track of the identity of user using a login token and user token. This context pair is initially determined at login and changes when you switch to a different database or when an impersonation takes place (user assumes permissions of different user). 