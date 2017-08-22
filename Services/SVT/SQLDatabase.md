<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>

</head><body>
<H2>SQLDatabase</H2>
<table>
<colgroup><col/><col/><col/></colgroup>
<tr><th>Description</th><th>ControlSeverity</th><th>Automated</th></tr>
<tr><td>Transparent data encryption (TDE) must be enabled:<br /><br />Azure SQL Database transparent data encryption helps protect against the threat of malicious activity by performing real-time encryption and decryption of the database, associated backups, and transaction log files at rest without requiring changes to the application</td><td>High</td><td>Yes</td></tr>
<tr><td>Enable SQL Server threat detection with email admins option. Do not exclude any detection types:<br />This setting helps to alert SQL Injection, SQL Injection Vulnerability, Login from new location, Unusual usage pattern and related attacks in a timely manner to take action.</td><td>High</td><td>Yes</td></tr>
<tr><td>Enable SQL Database threat detection with email admins option. Do not exclude any detection types:<br />This setting helps to alert SQL Injection, SQL Injection Vulnerability, Login from new location, Unusual usage pattern and related attacks in a timely manner to take action.</td><td>High</td><td>Yes</td></tr>
<tr><td>SQL Server firewall should be enabled:<br /> SQL Server level firewall rules enable you to prevent all access to your database server until you specify which IP ranges from internet are allowed and whether Azure applications can attempt to connect to your Azure SQL server </td><td>High</td><td>Yes</td></tr>
<tr><td>In a SQL Server with multiple databases, setup firewall rules also at the database level: <br />Database level firewall rules enable you to selectively grant access to just one of the databases in your Azure SQL server. Microsoft recommends using database-level firewall rules whenever possible to enhance security and to make your database more portable</td><td>High</td><td>No</td></tr>
<tr><td>Do not use SQL Authentication. Use AAD-authentication instead<br /> With Azure AD authentication, you can centrally manage the identities of database users and other Microsoft services in one central location. Central ID management provides a single place to manage database users and simplifies permission management </td><td>High</td><td>No</td></tr>
<tr><td>Client-side encryption should be used where appropriate</td><td>Medium</td><td>No</td></tr>
<tr><td>Access to SQL Servers and DBs must be granted in keeping with the principle of least privilege</td><td>Medium</td><td>No</td></tr>
<tr><td>Configure only the required IP addresses on SQL firewall. Do not use Any-to-Any IP range 0.0.0.0-255.255.255.255</td><td>High</td><td>Yes</td></tr>
<tr><td>Enable SQL Server audit with selected event types and retention period of minimum 365 days</td><td>Medium</td><td>Yes</td></tr>
<tr><td>Enable SQL Database audit with selected event types and retention period of minimum 365 days</td><td>Medium</td><td>Yes</td></tr>
<tr><td>Logs should be reviewed routinely</td><td>Medium</td><td>No</td></tr>
<tr><td>Use the latest version for SQL Database that is available</td><td>Low</td><td>Yes</td></tr>
</table>
</body></html>
