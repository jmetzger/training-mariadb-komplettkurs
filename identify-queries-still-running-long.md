# (Noch) laufende Queries mit langer Laufzeit identifizieren 


```
SELECT ID FROM information_schema.processlist WHERE Command = 'Query' AND Time > 60;
```

## Ref: 

  * https://releem.com/blog/managing-long-running-queries-in-mysql#rec746614581
