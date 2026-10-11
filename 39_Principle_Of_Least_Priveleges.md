# Principle of Least Privilege: Does Your App Really Need Admin Access?

Your reporting service only reads sales data. So why does its database account also have permission to delete customers?

Extra permissions are easy to grant during development—and easy to forget afterward. The principle of least privilege helps prevent that convenience from becoming a security problem.

## What does least privilege mean?

Give every user, application, and service only the permissions it needs to perform its job.

A service that reads reports needs access to the relevant data. It does not automatically need permission to update records, manage accounts, or change database tables. This matches NIST’s definition of granting only the minimum resources and authorizations required for a task.

The question is simple: **What does this identity actually need to do?**

## A practical example: a reporting service

Imagine an e-commerce application with a service that generates daily sales reports.

Its database access could look like this:

| Operation | Required? |
|---|---|
| Read approved sales data | Yes |
| Update order statuses | No |
| Delete customers | No |
| Create or drop tables | No |

Give the service a dedicated database account with access to the required tables, columns, or reporting views.

If someone steals its credentials, those credentials should not authorize destructive operations. However, the attacker could still read the data the account can access. Restricting which data it can read matters too.

Least privilege limits the damage a compromised identity can cause. It does not make compromise harmless.

## Apply it to users and resources

Permissions also need boundaries inside your application.

A customer may be allowed to view orders, but only their own orders. Checking that they are logged in is insufficient; the API must check whether they can access the specific order requested.

OWASP recommends denying access by default and validating permissions on every request. Hiding a button in the frontend does not enforce that rule—the backend must reject unauthorized actions.

## Avoid “give it admin so it works”

When an operation fails, granting administrator access can make the error disappear without explaining which permission was missing.

Instead, identify the operation, grant the specific permission it requires, and verify that unrelated operations remain blocked.

Use separate identities for separate responsibilities. A deployment process that changes the database schema has different needs from the application serving normal requests.

There is a tradeoff: narrower permissions require more planning and maintenance. Review them as responsibilities change, so old access does not accumulate.

## Key takeaway

Treat permissions as part of your application’s design. Give each identity the access its current responsibilities require, and verify that everything outside those boundaries is denied.