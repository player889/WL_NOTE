````ad-note
collapse: close
- Join Lines : Ctrl-J
- Block comment/uncomment : Ctrl-Q
- Go to matching brace : Ctrl-B
- Convert to lower case : Ctrl-U
- Convert to UPPER CASE : Ctrl-Shft-U
- Move Current Line Up : Ctrl-Shft-Up
- Close Current Document : Ctrl-W

```ad-tip
title: add "" to each line
in regular expression mode find and replace
~~~
(.+)
"\1"
~~~
```

```ad-tip
title: join line with comma
~~~
\r\n
,
~~~
```

```ad-tip
title: remove all line wihtout @ text
~~~
^[^@]*$
~~~
```


```ad-tip
title: add text to front of each row
~~~
^
~~~
```

```ad-tip
title: add text to end of each row
~~~
$
~~~
```

```ad-tip
title: 中文亂碼(garbled)
![[Pasted image 20230322153936.png|500]]

```


```ad-tip
title: first word uppercase
(?-s)^(.)(.*)
\L\1\E\2

```

```ad-tip
title: remove other lines without "imp
~~~reg
(?!^.*"imp.*$)^.+
~~~
```

```ad-tip
title: remove string after charactor each line
~~~reg
\.{3,}.*
~~~

- `\.{3,}`: Matches an ellipsis composed of three or more dots.
- `.*`: Matches any character (except a newline) that follows the ellipsis, which includes numbers, spaces, and any other characters till the end of each line.

```



````
