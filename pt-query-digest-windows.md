# Pt-query-digest under windows 

## Attention about download 

```
url is wrong in Reference document, us:
https://www.percona.com/get/pt-query-digest
```

## Walkthrough 

```
# Download and install msi-package - newest version
https://strawberryperl.com/
https://github.com/StrawberryPerl/Perl-Dist-Strawberry/releases/download/SP_54001_64bit_UCRT/strawberry-perl-5.40.0.1-64bit.msi
```

```
# Open this page 
https://www.percona.com/get/pt-query-digest
# Save as -> txt filetype -> pt-query-digest.pl in the mariadb/bin folder
```

![image](https://github.com/user-attachments/assets/ac6af0ee-61fe-4930-8371-6a76fec41db9)

```
# Please confirm with o.k.
```



```
Installed strawberry perl on my windows computer
Saved percona.com/get/pt-query-digest to a .pl file on my laptop
In the run part I typed cmd
In the new command window, I typed pt-query-digest.pl slow.log > digest.txt
```


## Reference 

  * http://www.jonathanlevin.co.uk/2012/01/query-digest-on-windows.html
