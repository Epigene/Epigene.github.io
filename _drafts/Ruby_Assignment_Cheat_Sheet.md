![assignment_matrix.png]({{ site.baseurl }}/images/assignment_matrix.png)
--> value [must be absent, irrelevant, must be present]
|
|
ˇ
receiver
[
must be absent
irrelevant
must be present
]


### 1;1 no change (permanently absent)

### 1;2 Only assign if blank

```ruby
receiver ||= value
```

### 1;3 only assign present values if existing value is blank

```ruby
receiver ||= value.presence

# i.e. will not change falsey to other falsey
```

TODO add rows 2 and 3
