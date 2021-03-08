---
title: Django 使用指南——使用表单
tags:
  - Django
  - Python
  - Web开发
id: '92'
categories:
  - - 编程学习
date: 2019-05-19 09:51:26
---

![](https://i.loli.net/2020/02/06/yUBWREsLAOYSwcQ.jpg) 表单是网站中最最常见和重要的功能了，可以实现与用户交互和交换数据。
<!-- more -->
基本都是看完官方文档后摘抄的，为了以后看的时候方便。**详见[官方文档](https://docs.djangoproject.com/zh-hans/2.2/topics/forms/)**。

# 字段数据

无论用表单提交了什么数据，一旦通过调用 `is_valid()` 验证成功（ `is_valid()` 返回 `True` ），已验证的表单数据将被放到 `form.cleaned_data` 字典中。这里的数据已经很好的为你转化为Python类型（字典）。 使用官方文档中的例子：

```python
# forms.py

from django import forms


class ContactForm(forms.Form):
    subject = forms.CharField(max_length=100)
    message = forms.CharField(widget=forms.Textarea)
    sender = forms.EmailField()
    cc_myself = forms.BooleanField(required=False)
```

```python
# views.py

from django.core.mail import send_mail


if form.is_valid():
    subject = form.cleaned_data['subject']
    message = form.cleaned_data['message']
    sender = form.cleaned_data['sender']
    cc_myself = form.cleaned_data['cc_myself']

    recipients = ['info@example.com']
    if cc_myself:
        recipients.append(sender)

    send_mail(subject, message, sender, recipients)
    return HttpResponseRedirect('/thanks/')
```

使用 `cleaned_data` 提取数据，不建议直接从 `request.POST`中获取。

# 手动渲染表单

使用上边的例子，写模板文件，单独渲染每项：

```markup
{ { form.non_field_errors } }
<div class="fieldWrapper">
    { { form.subject.errors } }
    <label for="{ { form.subject.id_for_label } }">Email subject:</label>
    { { form.subject } }
</div>
<div class="fieldWrapper">
    { { form.message.errors } }
    <label for="{ { form.message.id_for_label } }">Your message:</label>
    { { form.message } }
</div>
<div class="fieldWrapper">
    { { form.sender.errors } }
    <label for="{ { form.sender.id_for_label } }">Your email address:</label>
    { { form.sender } }
</div>
<div class="fieldWrapper">
    { { form.cc_myself.errors } }
    <label for="{ { form.cc_myself.id_for_label } }">CC yourself?</label>
    { { form.cc_myself } }
</div>
```

# 常用的 field 属性

## { { field.label } }

字段的label，比如 Email address。

## { { field.label\_tag } }

该字段的label封装在相应的 HTML `<label>` 标签中。它包含表单的 label\_suffix 。例如，默认的 label\_suffix 是一个冒号：

```markup
<label for="id_email">Email address:</label>
```

也可以自定义：

```bash
>>> f = ContactForm(auto_id='id_for_%s', label_suffix='')
>>> print(f.as_ul())
<li><label for="id_for_subject">Subject</label> <input id="id_for_subject" type="text" name="subject" maxlength="100" required></li>
<li><label for="id_for_message">Message</label> <input type="text" name="message" id="id_for_message" required></li>
<li><label for="id_for_sender">Sender</label> <input type="email" name="sender" id="id_for_sender" required></li>
<li><label for="id_for_cc_myself">Cc myself</label> <input type="checkbox" name="cc_myself" id="id_for_cc_myself"></li>

>>> f = ContactForm(auto_id='id_for_%s', label_suffix=' ->')
>>> print(f.as_ul())
<li><label for="id_for_subject">Subject -></label> <input id="id_for_subject" type="text" name="subject" maxlength="100" required></li>
<li><label for="id_for_message">Message -></label> <input type="text" name="message" id="id_for_message" required></li>
<li><label for="id_for_sender">Sender -></label> <input type="email" name="sender" id="id_for_sender" required></li>
<li><label for="id_for_cc_myself">Cc myself -></label> <input type="checkbox" name="cc_myself" id="id_for_cc_myself"></li>
```

## { { field.id\_for\_label } }

用于该字段的 ID（像上面的例子中的 `id_email` ）。如果您要手动构建 label，您可能要用这个来替换 `label_tag` 。例如，如果你有一些内嵌的 JavaScript 并且想要避免硬编码字段的 ID，这也很有用。

## { { field.value } }

字段的值。例如 `someone@example.com` 。

## { { field.html\_name } }

字段名称：用于其输入元素的 `name` 属性中。如果设置了表单前缀，它也会被加进去。

## { { field.help\_text } }

与该字段关联的帮助文本。

## { { field.errors } }

输出一个包含对应该字段所有验证错误信息的 `<ul class="errorlist">` 。 您可以用 `{ % for error in field.errors % }` 循环来自定义错误信息的显示。这种情况下，循环中的每个对象只是一个包含错误信息的简单字符串。

## { { field.is\_hidden } }

如果是隐藏字段，这个属性为 `True` ，否则为 `False` 。

## { { field.field } }

表单类中的 `Field` 实例由 `BoundField` 封装。您可以用它来访问 `Field` 的属性，比如 `{ { char_field.field.max_length } }` 。

# 复用表单模板

网站在多个位置对表单使用相同的渲染逻辑，可以将表单保存到独立的模板中，然后在其他模板中使用 `include` 标签来减少代码重复：

```markup
# In your form template:
# 调用表单模板
{ % include "form_snippet.html" % }


# In form_snippet.html:
# 可复用表单
{ % for field in form % }
    <div class="fieldWrapper">
        { { field.errors } }
        { { field.label_tag } } { { field } }
    </div>
{ % endfor % }
```

如果传递给模板的表单对象在上下文中具有不同的名称，您可以使用 `include` 标签的 `with` 属性来给它取别名。

```python
{ % include "form_snippet.html" with form=comment_form % }
```