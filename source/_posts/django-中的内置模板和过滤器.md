---
title: Django 中的内置模板和过滤器
tags:
  - Django
  - Python
  - Web开发
id: '89'
categories:
  - - 编程学习
date: 2019-05-20 21:49:38
---

![](https://i.loli.net/2020/02/06/yUBWREsLAOYSwcQ.jpg) Django 中的内置模板和过滤器。 [官方文档](https://docs.djangoproject.com/zh-hans/2.2/ref/templates/builtins/#ref-templates-builtins-tags)
<!-- more -->
# for

```python
<ul>
{ % for athlete in athlete_list % }
    <li>{ { athlete.name } }</li>
{ % endfor % }
</ul>
```

# for ... empty

```python
<ul>
{ % for athlete in athlete_list % }
    <li>{ { athlete.name } }</li>
{ % empty % }
    <li>Sorry, no athletes in this list.</li>
{ % endfor % }
</ul>
```

# if

```python
{ % if athlete_list % }
    Number of athletes: { { athlete_list|length } }
{ % elif athlete_in_locker_room_list % }
    Athletes should be out of the locker room soon!
{ % else % }
    No athletes.
{ % endif % }
```

允许在同一标记中使用 `and` 和 `or` 子句，`and` 优先级高于 `or` 例如：

```python
{ % if athlete_list and coach_list or cheerleader_list % }
```

将被解释为：

```python
if (athlete_list and coach_list) or cheerleader_list
```

# templatetag

由于模板系统没有“转义”的概念，因此要显示模板标签中使用的其中一个位，必须使用该标签。`{ % templatetag % }` 参数告诉输出哪个模板位：

争论

输出

openblock

{ %

closeblock

% }

openvariable

{ {

closevariable

} }

openbrace

{

closebrace

}

opencomment

{#

closecomment

#}

样品用法：

```python
{ % templatetag openblock % } url 'entry_list' { % templatetag closeblock % }
```