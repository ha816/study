To have  `launchd`  start  `mongod`  immediately and also restart at login, use:

```
$ brew services start mongodb-community

```

If you manage  `mongod`  as a service it will use the default paths listed above. To stop the server instance use:

```
$ brew services stop mongodb-community
```




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNTcxNzA3OTAsNzMwOTk4MTE2XX0=
-->