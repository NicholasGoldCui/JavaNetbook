# 第6章 字典
## 6.1 一个简单的字典
来看一个游戏，其中包含一些外星人，这些外星人的颜色和点数各不相同。下面式一个简单的字典，存储了有关特定外星人的信息：
```Python
alien_o = {'color':'green','points': 5}
print(alien_o['color'])
print(alien_o['points'])
```
与大多数编程概念一样，要熟练使用字典，也需要一段时间的练习。使用字典一段时间后，你就会明白为何它们能够高效地模拟现实世界中的情形。
## 6.2 使用字典
在Python中，字典是一系列键-值对。每个键都与一个值相关联，你可以使用键来访问与之相关联的值。与键相关联的值可以是数字、字符串、列表乃至字典。事实上，可将任何Python对象用作字典中的值。
在Python中，字典用放在花括号{}中的一系列键-值对表示，如前面的示例所示：
```Python
alien_o = {'color':'green','points':5}
```
键-值对是两个相关联的值。指定键时，Python将返回与之相关联的值。**键和值之间用冒号分隔，而键-值对之间用逗号**分隔。在字典中，你想存储多少个键-值对都可以。
### 6.2.1 访问字典中的值
要获取与键相关联的值，可依次指定字典名和放在方括号内的键，如下所示：
```Python
alien_0 = {'color':'green'}
print(alien_0['color'])
```
现在，你可以访问外星人alien_0的颜色和点数。如果玩家射杀了这个外星人，你就可以使用下面的代码来确定玩家应获得多少个点：
```Python
alien_0 = {'color':'green','points': 5}

new_points = alien_0['points']
print("You just earned " + str(new_points) + " points!")
```
如果你在有外星人被射杀时都运行这段代码，就会获取该外星人的点数。
### 6.2.2 添加键-值对
字典是一种动态结构，可随时在其中添加键-值对。要添加键-值对，可依次指定字典名、用方括号括起来的键和相关联的值。
下面在字典alien_0中添加两项信息：外星人的x坐标和y坐标，让我们能够在屏幕的特定位置显示该外星人。我们将这个外星人放在屏幕左边缘，且离屏幕边缘25像素的地方。由于屏幕坐标系的原点通常为左上角，因此要将该外星人放在屏幕左边缘，可将x坐标设置为0；要将该外星人放在离屏幕顶部25像素的地方，可将y坐标设置为25，如下所示：
```Python
alien_0 = {'color':'green','points': 5}
print(alien_0)

alien_0['x_position'] = 0
alien_0['y_position'] = 25
print(alien_0)
```
**注意**
键-值对的排列顺序与添加顺序不同。Python不关心键-值对的添加顺序，而只关心键和值之间的关联关系。
### 6.2.3 先创建一个空字典
有时候，在空字典中添加键-值对是为了方便，而有时候必须这样做。为此，可先使用一对空的花括号定义一个字典，再分行添加各个键-值对。例如，下例演示了如何以这种方式创建字典alien_0:
```Python
alien_0 = {}

alien_0['color'] = 'green'
alien_0['points'] = 5

print(alien_0)
```
使用字典来存储用户提供的数据或在编写能自动生成大量键-值对的代码时，，通常都需要先定义一个空字典。
### 6.2.4 修改字典中的值
要修改字典中的值，可依次指定字典名、用方括号括起来的键以及与该键相关联的新值。例如，假设随着游戏的进行，需要将一个外星人从绿色改为黄色：
```Python
alien_0 = {'color':'green'}
print("The alien is " + alien_0['color'] + ".")

alien_0['color'] = 'yellow'
print("The alien is now " + alien_0['color'] + ".")
```
来看一个更有趣的例子：对一个能够以不同速度移动的外星人的位置进行跟踪。为此，我们将存储该外星人的当前速度，并据此确定该外星人将向右移动多元：
```Python
alien_0 = {'x_position': 0, 'y_position': 25, 'speed':'medium'}
print("Original x-position: " + str(alien_0['x_position']))

# 向右移动外星人
# 据外星人当前速度决定将其移动多远
if alien_0['speed'] == 'slow':
    x_increment = 1
elif alien_0['speed'] == 'medium':
    x_increment = 2
else:
    # 这个外星人的速度一定很快
    x_increment = 3
# 新位置等于老位置加上增量
alien_0['x_position'] = alien_0['x_position'] + x_increment
```
### 6.2.5 删除键-值对
对于字典不再需要的信息，可使用del语句将相应的键-值对彻底删除。使用del语句时，必须指定字典名和要删除的键。
例如，下面的代码从字典alien_0中删除键'points'及其值：
```Python
alien_0 = {'color':'green','points': 5}
print(alien_0)

del alien_0['points']
print(alien_0)
```
**注意** 删除该键的同时删除与该键相关联的值。删除的键-值对永远消失了。
### 6.2.6 有类似对象组成的字典
再前面的示例中，字典存储的是一个对象(游戏中的一个外星人)的多种信息，但你也可以使用字典来存储众多对象的同一种信息。例如，假设你要调查很多人，询问他们最喜欢的编程语言，可使用一个字典来存储这种简单调查的结果，如下所示:
```Python
favorite_languages = {
    'jen':'python',
    'sarah':'C',
    'edward':'ruby',
    'phil':'python',
}
```
正如你看到的，我们将一个较大的字典放在了多行中，其中每个键都是一个被调查的名字，而每个值都是被调查者喜欢的语言。确定需要使用多行来定义字典时，在输入左花括号后按回车键，再在下一行缩进四个空格，指定第一个键-值对，并在它后面加上一个逗号。此后你再次按回车键时，文本编辑器将自动缩进后续键-值对，且缩进量与第一个键-值对相同。
定义好字典后，在最后一个键-值对的下以行添加一个右花括号，并缩进四个空格，使其与字典中的键对齐。另外一种不错的做法是在最后一个键-值对后面也加上逗号，为以后在下一行添加键-值对做好准备。
**注意** 对于较长的列表和字典，大多数编辑器都有以类似方式设置其格式的功能。对于较长的字典，还有其他一些可行的格式设置方式，因此你在编辑器或其他源代码中，你可能会看到稍微不同的格式设置方式。
给定被调查者的名字，可使用这个字典轻松地获悉他喜欢的语言：
```Python
favorite_languages = {
    'jen':'python',
    'saran':'c',
    'edward':'ruby',
    'phil':'python',
}

print("Sarah's favorite language is " + favorite_languages['Sarah'].title() + ".")
```
## 6.3 遍历字典
一个Python字典可能只包含几个键-值对，也可能包含数百万-值对。鉴于字典可能包含大量的数据，Python支持对字典遍历。字典可用于以各种方式存储信息，因此由多种遍历字典的方式：可遍历字典的所有键-值对、键或值。
### 6.3.1 遍历所有的键-值对
探索各种遍历方法前，先来看一个新字典，它用于存储有关网站用户的信息。下面的字典存储一名用户的用户名、名和姓：
```Python
user_0 = {
    'suername':'efermi',
    'first':'enrico',
    'last':'fermi',
}
```
利用本章前面介绍过的知识，可访问user_0的任何一项信息，但如果要获悉该用户字典中的所有信息，该怎么办呢？可以使用一个for循环来遍历这个字典：
```Python
user_0 = {
    'username':'efermi',
    'first':'enrico',
    'last':'fermi',
}

for key, value in user_0.items():
    print("\nKey: " + key)
    print("Value: " + value)
```
**注意** 即便遍历字典时，键-值对的返回顺序也与存储顺序不同。Python不关心键-值对的存储顺序，而只跟踪键和值之间的关联关系。
即便字典存储的是上千万至上百万人的调查结果，着中循环也管用。
### 6.3.2 遍历字典中的所有键
在不需要使用字典中的值时，方法keys()很有用。下面来遍历字典favorite_languages，并将每个被调查者的名字都打印出来：
```Python
favorite_languages = {
    'jen':'python',
    'sarah':'c',
    'edward':'ruby',
    'phil':'python',
}
for name in favorite_languages.keys():
    print(name.title())
```
for循环处的代码让python提取字典favorite_languages中的所有键，并依次将它们存储到变量name中。输出列出了每个被调查者的名字。
### 6.3.3 按顺序遍历字典中的所有键
字典总是明确地记录键和值之间的关联关系，但获取字典的元素时，获取顺序是不可预测的。这不是问题，因为通常你想要的只是获取与键相关联的正确的值。
要以特定的顺序返回元素，一种办法是在for循环中对返回的键进行排序。为此，可使用函数sorted()来获得按特定顺序排列的键列表的副本：
```Python
favorite_languages = {
    'jen':'python',
    'sarah':'c',
    'edward':'ruby',
    'phil':'python',
}

for name in sorted(favorite_languages.keys()):
    print(name.title() + ", thank you for taking the poil.")
```
### 6.3.4 遍历字典中的所有值
如果你感兴趣的主要是字典中包含的值，可使用方法values()，它返回一个值列表，而不包含任何键。例如，如果我们想获得一个这样的列表，即其中只包含被调查者选择的各种语言，而不包含被调查者的名字，可以这样做：
```Python
favorite_languages = {
    'jen':'python',
    'sarah':'c',
    'edward':'ruby',
    'phil':'python',
}

print("The following languages have been mentioned:")
for language in favorite_languages.values():
    print(language.title())
```
这种做法提取字典中所有的值，而没有考虑是否重复。涉及的值很少时，这也许不是问题，但如果被调查者很多，最终的列表可能包含大量的重复项。为剔除重复项，可使用集合(set)。**集合** 类似于列表，但每个元素都必须是独一无二的：
```Python
favorite_languages = {
    'jen':'python',
    'sarah':'c',
    'edward':'ruby',
    'phil':'python',
}

print("The following languages have been mentioned:")
for language in set(favorite_languages.values()):
    print(language.title())
```
结果是不重复的列表，其中列出了被调查者提及的所有语言，随着你更深入地学习Python，经常会发现它内置的功能可帮助你以希望的方式处理数据。
## 6.4 嵌套
有时候，需要将一系列字典存储在列表中，或将列表作为值存储在字典中，这称为**嵌套**。你可以在列表中嵌套字典、在字典中嵌套列表甚至在字典中嵌套字典。
### 6.4.1 字典列表
字典alien_0包含一个外星人的各种信息，但无法存储第二个外星人的信息，更别说屏幕上全部外星人的信息了。如何管理成群结队的外星人呢？一种办法是创建一个外星人列表，其中每个外星人都是一个字典，包含有关该外星人的各种信息。例如，下面的代码创建一个包含三个外星人的列表：
```Python
alien_0 = {'color':'green','points': 5}
alien_1 = {'color':'yellow','points': 10}
alien_2 = {'color':'red','points': 15}

aliens = [alien_0, alien_1, alien_2]

for alien in aliens:
    print(alien)
```
我们首先创建了三个字典，其中每个字典都表示一个外星人。在aliens处，我们将这些字典都放到一个名为aliens的列表中。最后，我们遍历这个列表，并将外星人都打印出来。
更符合现实的情形是，外星人不止三个，且每个外星人都是使用代码自动生成的。在下面的示例中，我们使用range()生成了30个外星人：
```Python
# 创建一个存储外星人的空列表
aliens = []

# 创建30个绿色的外星人
for alien_number in range(30):
    new_alien = {'color':'green','points': 5, 'speed':'slow'}
    aliens.append(new_alien)

# 显示前五个外星人
for alien in aliens[:5]:
    print(alien)
print("……")

# 显示创建了多少个外星人
print("Thtal number of aliens: " + str(len(aliens)))
```
在这个示例中，首先创建了一个空列表，用于存储接下来将创建的所有外星人。在alien_number处，range()返回一系列数字，其唯一的用途是告诉Python我们要重复这个循环多少次。每次执行这个循环时，都创建一个外星人，并将其附加到列表aliens末尾。在alien处，使用切片来打印前五个外星人；在print处，打印列表的长度，以证明确实创建了30个外星人。
这些外星人都具有相同的特征，但在Python看来，每个外星人都时对立的，这然我们能够独立地修改每个外星人。
在什么情况下需要处理成群结队的外星人呢？想象一下，可能随着游戏的进行，有些外星人会变色且移动速度会加快。必要时，我们可以使用for循环和if语句来修改某些外星人的颜色。例如，要将前三个外星人修改为黄色的、速度为中等且值10个点，可以这样做：
```Python
# 创建一个用于存储外星人的空列表
aliens = []

# 创建30个绿色的外星人
for alien_number in range(0,30):
    new_alien = {'color':'green', 'points': 5, 'speed':'slow'}
    aliens.append(new_alien)

for alien in alien[0:3]:
    if alien['color'] == 'green':
        alien['color'] = 'yellow'
        alien['speed'] = 'medium'
        alien['points'] = 10

# 显示前五个外星人
for alien in aliens[0:5]:
    print(alien)
print("……")
```
你可以进一步扩展这个循环，在其中添加一个elif代码块，将黄色外星人改为移动速度快且值15个点的红色外星人，如下所示(这里只列出了循环，而没有列出整个程序):
```Python
for alien in aliens[0:3]:
    if alien['color'] == 'green':
        alien['color'] = 'yellow'
        alien['speed'] = 'medium'
        alien['points'] = 10
    elif alien['color'] == 'yellow':
        alien['color'] = 'red'
        alien['speed'] = 'fast'
        alien['points'] = 15
```
经常需要在列表中包含大量的字典，而其中每个字典都包含特定对象的众多信息。例如，你可能需要为网站的每个用户创建一个字典(就像6.3.1节那样)，并将这些字典存储在一个名为users的列表中。在这个列表中，所有字典的结构都相同，因此你可以遍历这个列表，并以相同的方式处理其中的每个字典。
### 6.4.2 在字典中存储列表
有时候，需要将列表存储在字典中，而不是将字典存储在列表中。例如，你如何描述顾客点的比萨呢？如果使用列表，只能存储要添加的比萨配料；但如果使用字典，就不仅可在其中包含配料列表，还可包含其他有关比萨的描述。
在下面的示例中，存储了比萨的两方面信息：外皮类型和配料列表。其中的配料列表是一个与键'toppings'相关联的值。要访问该列表，我们使用字典名和键'toppings'，就像访问字典中的其他值一样。这将返回一个配料列表，而不是单个值：
```Python
# 存储所点比萨的信息
pizza = {
    'crust':'thick',
    'toppings':['mushrooms', 'extra cheese'],
}

# 概述所点的比萨
print("You ordered a " + pizza['crust'] + "-crust pizza " + "with the following toppings:")

for topping in pizza['toppings']:
    print("\t" + topping)
```
每当需要在字典中将一个键关联到多个值时，都可以在字典中嵌套一个列表。在本章前面有关喜欢的编程语言的示例中，如果将每个人的回答都存储在一个列表中，被调查者就可选择多种喜欢的语言。在这种情况下，当我们遍历字典时，与每个被调查者相关联的都是一个语录列表，而不是一种语言；因此，在遍历该字典的for循环中，我们需要在使用一个for循环来遍历与被调查者相关联的语言列表：
```Python
favorite_languages = {
    'jen':['python','ruby'],
    'sarah':['c'],
    'edward':['ruby','go'],
    'phil':['python','haskell'],
}

for name, languages in favorite_languages.items():
    print("\n" + name.title() + " 's favorite languages are:")
    for language in languages:
        print("\t" + language.title())
```
为进一步改进这个程序，可在遍历字典的for循环开头添加一条if语句，通过查看len(languages)的值来确定当前的被调查者喜欢的语言是否有多种。如果他喜欢的语言有多种，就像以前一样显示输出；如果只有一种，就相应修改输出的措辞，如显示Sarah's favorite language is C。
**注意** 列表和字典的嵌套层级不应太多。如果嵌套层级比前面的示例多得多，很有可能更简单的解决问题的方案。
### 6.4.3 在字典中存储字典
可在字典中嵌套字典，但这样做时，代码可能很快复杂起来。例如，如果有多个网站用户，每个都有独特的用户名，可在字典中将用户名作为键，然后将每位用户的信息存储在一个字典中，并将该字典作为与用户名相关联的值。在下面的程序中，对于每位用户，我们都存储了其他三项信息：名、姓和居住地；为访问这些信息，我们遍历所有的用户名，并访问与每个用户名相关联的信息字典：
```Python
users = {
    'aeinstein':{
        'first':'albert',
        'last':'einstein',
        'location':'princeton',
    },

    'mcurie':{
        'first':'marie',
        'last':'curie',
        'location':'paris',
    }
}

for username, user_info in user.items():
    print("\hUsername: " + username)
    full_name = user_info['first'] + " " + user_info['last']
    location = user_info['location']

print("\tFull name: " + full_name.title())
print("\tLocation: " + location.title())
```
**请注意**表示每位用户的字典的结构都相同，虽然Python并没有这样的要求，但这使得嵌套的字典处理起来更容易。倘若表示每位用户的字典都包含不同的键，for循环内部的代码将更复杂。
# 第7章 用户输入和while循环
大多数程序都旨在解决最终用户的问题，为此通常需要从用户那里获取一些信息。
## 7.1 函数input()的工作原理
函数input()让程序暂停运行，等待用户输入一些文本。获取用户输入后，Python将其存储在一个变量中，以方便你使用。
例如，下面的程序让用户输入一些文本，再将这些文本呈现给用户：
```Python
message = input("Tell me something, and I will repeat it back to you: ")
print(message)
```
函数input()接受一个参数：即要向用户显示的提示或说明，让用户知道该如何做。再这个示例中，Python运行第1行代码时，用户将看到提示Tell me something, and I will repeat it back to you: 。程序等待用户输入，并在用户按回车键后继续运行。输入存储在变量message中，接下来的print(message)将输入呈现给用户：
**注意** Sublime Text不能运行提示用户输入的程序。你可以使用Sublime Text来编写提示用户输入的程序，但必须从终端运行它们。
### 7.1.1 编写清晰的程序
每当你使用函数input()时，都应指定清晰而易于明白的提示，准确地指出你希望用户提供什么样的信息——指出用户该输入任何信息提示都行，如下所示：
```Python
name = input("Please enter your name: “)
print("Hello, " + name + "!")
```
通过在提示末尾(这里是冒号后面)包含一个空格，可将提示于用户输入分开，让用户清楚地知道其输入始于何处。
有时候，提示可能超过一行，例如，你可能需要指出获取特定输入的原因。在这种情况下，可将提示存储在一个变量中，再将该变量传递给函数input()。这样，即便提示超过一行，input()语句也非常清晰。
```Python
prompt = "If you tell us who you are, we can personalize the messages you see."
prompt += "\nWhat is your first name? "

name = input(prompt)
print("\nHello, " + name + "!")
```
### 7.1.2 使用int()来获取数值输入
使用函数input()时，Python将用户输入解读为字符串。请看下面让用户输入其年龄的解释器会话：
```Python
>>> age = input("How old are you? ")
How old are you? 21
>>> age
'21'
```
用户输入的是数字21，但我们请求Python提供变量age的值时，它返回的是'21'——用户输入的数值的字符串表示。我们怎么知道Python将输入解读成了字符串呢？因为这个数字用引号括起来了。如果我们只想打印输入，这一点问题都没有；但如果你试图将输入作为数字使用，就会引发错误。

如何再实际程序中使用函数int()呢？请看下面的程序，它判断一个人是否满足坐过山车的身高要求：
```Python
height = input("How tall are you, in inches? ")
height = int(height)

if height >= 36:
    print("\nYou're tall enough to ride!")
else:
    print("\nYou'll be able to ride when you're a little older.")
```
### 7.1.3 求模运算符
处理数值信息时，求模运算符(%)是一个很有用的工具，它将两个数相除并返回余数：
```Python
>>> 4 % 3
1
>>>5 % 3
2
```
求模运算符不会指出一个数是另一个数的多少倍，而只指出余数是多少。
如果一个数可被另一个数整除，余数就为0，因此求模运算符将返回0。
### 7.1.4 再Python2.7中获取输入
如果你使用的时Python2.7，应使用函数raw_input()来提示用户输入。这个函数与python3中的input()一样，也将输入解读为字符串。
## 7.2 while循环简介
for循环用于针对集合中的每个元素都一个代码块，而while循环不断地运行，直到指定的条件不满足为止。
### 7.2.1 使用while循环
你可以使用while循环来数数，例如，下面的while循环从1数到5：
```Python
current_number = 1
while current_number <= 5:
    print(current_number)
    current_number += 1
```
### 7.2.2 让用户选择何时退出
可使用while循环让程序再用户愿意时不断地运行，如下面的程序所示。我们再其中定义了一个退出值，只要用户输入的不是这个值，程序就接着运行：
```Python
prompt = "\nTell me something, and I will repeat it back to you:"
prompt += "\nEnter 'quit' to end the program."
message = ""
while message != 'quit':
    message = input(prompt)
    print(message)
```
### 7.2.3 使用标志
在前一个示例中，我们让程序在满足指定条件时就执行特定的任务。但在更复杂的程序中，很多不同的事件都会导致程序停止运行；在这种情况下，该怎么办呢？
例如，在游戏中，多种事件都可能导致游戏结束，如玩家一艘飞船都没有了或要保护的城市都被摧毁了。导致程序结束的事件有很多时，如果在一条while语句中检查所有这些条件，将既复杂又困难。
在要求很多条件都满足才继续运行的程序中，可定义一个变量，用于判断整个程序是否处于活动状态。这个变量被称为**标志**，充当了程序的交通信号灯。你可让程序在标志为True时继续运行，并在任何事件导致标志的值为False时让程序停止运行。这样，在while语句中就只需检查一个条件——标志的当前值是为True，并将所有测试(是否发生了应将标志设置为False的事件)都放在其他地方，从而让程序变得更为整洁。
下面来在前一节的程序中添加一个标志。我们把这个标志命名为active(可给它指定任何名称),它将用于判断程序是否应继续运行：
```Python
prompt = "\nTell me something, and I will repeat it back to you:"
prompt += "\nEnter 'quit' to end the program. "

active = True
while active:
    message = input(prompt)

    if message == 'quit':
        active = False
    else:
        print(message)
```
我们将变量active设置成了True，让程序最初处于活动状态。这样就简化了while语句，因为不需要在其中做任何比较——相关的逻辑由程序的其他部分处理。只要变量active为True，循环就将继续运行。
### 7.2.4 使用break退出循环
要立即退出while循环，不再运行循环中余下的代码，也不管条件测试的结果如何，可使用break语句。break语句用于控制程序流程，可使用它来控制那些代码将执行，那些代码不执行，从而让程序按你的要求执行你要执行的代码。
例如，来看一个让用户指出他到过哪些地方的程序。在这个程序中，我们可以用户输入'quit'后使用break语句立即退出while循环：
```Python
prompt = "\nPlease enter the name of a city you have visited:"
prompt += "\n(Enter 'quit' when you are finished.)"

while True:
    city = input(prompt)

    if  city = 'quit':
        break
    else:
        print("I'd love to go to " + city.title() + "!")
```
**注意** 在任何Python循环中都可以使用break语句。例如，可使用break语句来退出遍历列表或字典的for循环。
### 7.2.5 在循环中并使用continue
要返回到循环开头，并根据条件测试结果决定是否继续执行循环，可使用continue语句，它不像break语句那样不再执行余下的代码并退出整个循环。例如，来看一下从1数到10，但只打印其中偶数的循环：
```Python
curren_number = 0
while current_number < 10:
    current_number += 1
    if current_number % 2 == 0:
        countinue
    
    print(current_number)
```
### 7.2.6 避免无限循环
每个while循环都必须有停止运行的途径，这样才不会没完没了地执行下去。例如，下面的循环从1数到5：
```Python
x = 1
while x <= 5:
    print(x)
    x += 1
```
**注意** 有些编辑器(如Sublime Text)内嵌了输出窗口，这可能导致难以结束无限循环，因此不得不关闭编辑器来结束无限循环。
## 7.3 使用while循环来处理列表和字典
到目前为止，我们每次都只处理了一项用户信息：获取用户的输入，再将输入打印出来或做出应答；循环再次运行时，我们获悉另一个输入值并作出响应。然而，要记录大量的用户和信息，需要在while循环中使用列表和字典。
for循环是一种遍历列表的有效方式，但在for循环中不应该修改列表，否则将导致Python难以跟踪其中的元素。要在遍历列表的同时对其进行修改，可使用while循环。通过将while循环同列表和字典结合起来使用，可收集、存储并组织大量输入，供以后查看和显示。
### 7.3.1 在列表之间移动元素
假设有一个列表，其中包含新注册但还未验证的网站用户；验证这些用户后，如何将他们移到另一个已验证用户列表中呢？代码可能类似于下面这样：
```Python
# 首先，创建一个待验证用户列表
# 和一个用于存储已验证用户的空列表
unconfirmed_users = ['alice', 'brian', 'candace']
confirmed_users = []

# 验证每个用户，直到没有未验证用户为止，将每个经过验证的列表都移到已验证用户列表中
while unconfirmed_users:
    current_user = unconfirmed_users.pop()

    print("Verifying user: " + current_user.title())
    confirmed_users.append(current_user)

# 显示所有已验证的用户
print("\nThe following users have been confirmed:")
for confirmed_user in confirmed_users:
    print(confirmed_user.title())
```
### 7.3.2 删除包含特定值的所有列表元素
在第3章中，我们使用函数remove()来删除列表中的特定值，这之所以可行，是因为要删除的值在列表中只出现了一次。如果要删除列表中所有包含特定值的元素，该怎么办呢？
假设你有一个宠物列表，其中包含多个值为'cat'的元素。要删除所有这些元素，可不断运行一个while循环，直到列表中不再包含值'cat'，如下所示：
```Python
pets = ['dog', 'cat', 'dog', 'goldfish', 'cat', 'rabbit', 'cat']
print(pets)

while 'cat' in pets:
    pets.remove('cat')

print(pets)
```
### 7.3.3 使用用户输入来填充字典
可使用while循环提示用户输入任意数量的信息。下面来创建一个调查程序，其中的循环每次执行时都提示输入被调查者的名字和回答。我们将收集的数据存储在一个字典中，以便将回答同被调查者关联起来：
```Python
# 设置一个标志，指出调查是否继续
polling_active = True

while polling_active:
    # 提示输入被调查者的名字和回答
    name = input("\nWhat is your name? ")
    response = input("Which mountain would you like to climb someday? ")

    # 将答卷存储在字典中
    responses[name] = response

    # 看看是否还有人要参与调查
    repeat = input("Would you like to let another person respond? (yes/ no) ")
    if repeat == 'no':
        polling_active = False

# 调查结束，显示结果
print("\n--- Poll Results ---")
for name, response in responses.items():
    print(name + " would like to climb " + response + ".")
```
# 第8章 函数
函数是带名字的代码块，用于完成具体的工作。
要执行函数定义的特定任务，可调用该函数。需要在程序中多次执行同一项任务时，你无需反复编写完成该任务的代码，而只需调用执行该任务的函数，让Python运行其中的代码。你将发现，通过使用函数，程序的编写、阅读、测试和修复都将更容易。
## 8.1 定义函数
下面是一个打印问候语的简单函数，名为great_user():
```Python
def greet_user():
    """显示简单的问候语"""
    print("Hello!)

greet_user()
```
这个示例演示了最简单的函数结构。def处的代码行使用关键字def来告诉Python你要定义一个函数。这是**函数**定义，向Python指出了函数名，还可能在括号内指出函数为完成其任务需要什么样的信息。在这里，函数名为greet_user()，它不需要任何信息就能完成其工作，因此括号是空的(即便如此，括号也必不可少)。最后，定义以冒号结尾。
紧跟在def greet():后面的所有缩进进行构成了函数体。注释处的文本是被称为**文档字符串(docstring)**的注释，描述了函数是做什么的。文档字符串用三引号括起，Python使用它们来生成有关程序中函数的文档。
代码行print("Hello!")是函数体内的唯一一行代码，greet_user()只做一项工作：打印Hello!。
要使用这个函数，可调用它。**函数调用**让Python执行函数的代码。要**调用**函数，可依次指定函数名以及用括号括起的必要信息，如greet_user()处所示。由于这个函数不需要任何信息，因此调用它时只需要输入greet_user()即可。和预期的一样，它打印Hello!。
### 8.1.1 向函数传递信息
只需稍作修改，就可以让函数greet_user()不仅向用户显示Hello！，还将用户的名字用作抬头。为此可在函数定义def greet_user()的括号内添加username。通过在这里添加username,就可让函数接受你给username指定的任何值。现在，这个函数要求你调用它时给username指定一个值。调用greet_user()时，可将一个名字传递给它，如下所示：
```Python
def greet_user(username):
    """显示简单的问候语"""
    print("Hello, " + username.title() + "!")

greet_user('jesse')
```
代码greet_user('jesse')调用函数greet_user()并向它传递'sarah',打印Hello, Sarah!。你可以根据需要调用函数greet_user()任意次，调用时无论传入什么样的名字，都会生成相应的输出。
### 8.1.2 实参和形参
前面定义函数greet_user()时，要求给变量username指定一个值。调用这个函数并提供这种信息(人名)时，它将打印相应的问候语。

在函数greet_user()的定义中，变量username是一个**形参**——函数完成其工作所需的一项信息。在代码greet_user('jesse')中，值'jesse'是一个**实参**。实参是调用函数时传递给函数的信息。我们调用函数时，将要让函数使用的信息放在括号内。在greet_user('jesse')中，将实参'jesse'传递给了函数greet_user()，这个值被存储在形参username中。
**注意** 大家有时候会形参、实参不分，因此如果你看到有人将函数定义中的变量称为实参或将函数调用中的变量称为形参，不要大惊小怪。
## 8.2 传递实参
鉴于函数定义时可能包含多个形参，因此函数调用中也可能包含多个实参。向函数传递实参的方式很多，可使用**位置实参**，这要求实参的顺序与形参的顺序相同；也可使用**关键字实参**，，其中每个实参都有变量名和值组成；还可使用列表和字典。下面来依次介绍这些方式。
### 8.2.1 位置实参
你调用函数时，Python必须将函数调用中的每个实参都关联到函数定义中的一个形参。为此，最简单的关联方式是基于实参的顺序。这种关联方式被称为**位置实参**。为明白其中的工作原理，来看一个显示宠物信息的函数。这个函数指出一个宠物属于那种动物以及它叫什么名字，如下所示：
```Python
def describe_pet(animal_type, pet_name):
    """显示宠物的信息"""
    print("\nI have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")

describle_pet('hamster', 'harry')
```
1. 调用函数多次
你可以根据需要调用函数任意次。要再描述一个宠物，只需再次调用describle_pet()即可：
```Python
def describe_pet(animal_type, pet_name):
    """显示宠物的信息"""
    print("\nI have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")

describe_pet('hamster', 'harry')
describe_pet('dog', 'whillie')
```
再函数中，可根据需要使用任意数量的位置实参，Python将按顺序将函数调用中的实参关联到函数定义中相应的形参。
2. 位置实参的顺序很重要
使用位置实参来调用函数时，如果实参的顺序不正确，结果可能出乎意料：
### 8.2.2 关键字实参
**关键字实参**是传递给函数的名称——值对。你直接在实参中将名称和值关联起来了，因此向函数传递实参时不会混淆(不会得到名为Hamster的harry这样的结果)。关键字实参让你无需考虑函数调用中的实参顺序，还清楚地指出了函数调用中各个值的用途。
下面来重新编写pets，在其中使用关键字实参来调用describle_pet():
```Python
def describe_pet(animal_type, pet_name):
    """显示宠物的信息"""
    print("\nI have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")

describe_pet(animal_type = 'hamster', pet_name = 'harry')
```
函数describe_pet()还是原来那样，但调用这个函数时，我们向Python明确地指出了各个实参对应的形参。看到这个函数调用时，Python直到应该将实参'hamster'和'harry'分别存储在形参animal_type和pet_name中。输出正确无误，它指出我们有一只名为Harry的仓鼠。

关键字实参的顺序无关紧要，因为Python知道各个值该存储到哪个形参中。下面两个函数调用时等效的：
```Python
describe_pet(animal_type = 'hamster', pet_name = 'harry')
describe_pet(pet_name = 'harry', animal_type = 'hamster')
```
**注意** 使用关键字实参时，务必准确的指定函数定义中的形参名。
### 8.2.3 默认值
编写函数时，可给每个形参指定**默认值**。在调用函数中给形象提供了实参时，Python将使用指定的实参值；否则，将使用形参的默认值。因此，给形参指定默认值后，可在函数调用中省略相应的实参。使用默认值可简化函数调用，还可清楚地指出函数的典型用法。
例如，如果你发现调用describle_pet()时，描述的大都是小狗，就可将形参animal_type的默认值设置为'dog'。这样，调用describle_pet()来描述小狗时，就可不提供这种信息：
```Python
def describe_pet(pet_name, animal_type = 'dog'):
    """显示宠物的信息"""
    print("\nI have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")

describe_pet(pet_name = "willie')
```
这里修改了函数describle_pet()的定义，在其中给形参animal_type指定了默认值'dog'。这样，调用这个函数时，如果没有给animal_type指定值，Python将把这个形参设置为'dog'。
**请注意**，在这个函数的定义中，修改了形参的排列顺序。由于给animal_type指定了默认值，无需通过实参来指定动物类型。因此在函数调用中只包含一个实参——宠物的名字。然而，Python依然将这个实参视为位置实参，因此如果函数调用中只包含宠物的名字，这个实参将关联到函数定义中的第一个形参。这就需要将pet_name放在形参列表开头的原因所在。
如果要描述的动物不是小狗，可使用类似于下面的函数调用：
```Python
describe_pet(pet_name = 'harry', animal_type = 'hamster')
```
由于显示地给animal_type提供了实参，因此Python将忽略这个形参的默认值。
**注意** 使用默认值时，在形参列表中必须先列出没有默认值的形参，再列出的默认值的实参。这让Python依然能够正确地解读位置实参。
### 8.2.4 等效的函数调用
鉴于可混合使用位置实参、关键字实参和默认值，通常有多种等效的函数调用方式。请看下面的函数describle_pets()的定义，其中给一个形参提供了默认值：
```Python
def describe_pet(pet_name, animal_type = 'dog')
```
基于这种定义，再任何情况下都必须个pet_name提供实参；指定该实参时可以使用位置方式，也可以使用关键字方式。如果要描述的动物不是小狗，还必须再函数调用中给animal_type提供实参；同样，指定该实参时可以使用位置方式，也可以使用关键字方式。下面对这个函数的所有调用都可行：
```Python
# 一条名为Willie的小狗
describe_pet('willie')
describe_pet(pet_name = 'willie')

# 一只名为Harry的仓鼠
describe_pet('harry', 'hamster')
describe_pet(pet_name = 'harry', animal_type = 'hamster')
describe_pet(animal_type = 'hamster', pet_name = 'harry')
```
这些函数调用的输出与前面的示例相同。
**注意** 使用那种调用方式无关紧要，只要函数调用能生成你希望的输出就行。使用对你来说最容易理解的调用方式即可。

### 8.2.5 避免实参错误
等你开始使用调用函数后，如果遇到实参不匹配错误，不要大惊小怪。你提供的实参多于或少于函数完成其工作所需的信息时，将出现实参不匹配错误。例如，如果调用函数describe_pet()时没有指定任何实参，结果是Python发现该函数调用缺少必要的信息，而traceback(回溯)指出了这一点。

## 8.3 返回值
函数并非总是直接显示输出，相反，它可以处理一些数据，并返回一个或一组值。函数返回的值被称为**返回值**。再函数中，可使用return语句将值返回到调用函数的代码行。
返回值让你能够将程序的大部分繁重工作移到函数中去完成，从而简化主程序。

### 8.3.1 返回简单值
下面来看一个函数，它接受名和姓并返回整洁的姓名：
```Python
def get_formatted_name(first_name, last_name):
    """返回整洁姓名"""
    full_name = frst_name + ' ' + last_name
    return full_name.title()

musician = get_formatted_name('jimi', 'hendrix')
print(musician)
```

### 8.3.2 让实参变成可选的
有时候，需要让实参变成可选的，这样使用函数的人就只需要必要时才提供额外的信息。可使用默认值来让实参变成可选的。
例如，假设我们要扩展函数get_formatted_name()，使其还处理中间名。为此，可将其修改成类似于下面这样：
```Python
def get_formatted_name(first_name, middle_name, last_name):
    """返回整洁的姓名"""
    full_name = first_name + ' ' + middle_name + ' ' + last_name
    return full_name.title()

musician = get_formatted_name('john', 'lee', 'hooker')
print(musician)
```
只要同时提供名、中间名和姓，这个函数就能正确地运行。它根据这三个部分创建一个字符串，再适当的地方加上空格，并将结果转换为首字母大写格式。

然而，并非所有的人都有中间名，但如果你调用这个函数时只提供了名和姓，它将不能正确地运行。为让中间名变成可选的，可给实参middle_name指定一个默认值——空字符串，并在用户没有提供中间名时不使用这个实参。为让get_formatted_name()在没有提供中间名时依然可行，可给实参middle_name指定一个默认值——空字符串，并将其移到形参列表的末尾：
```Python
def get_formatted_name(first_name, last_name, middle_name = ''):
    """返回整洁的姓名"""
    if middle_name:
        full_name = first_name + ' ' + middle_name + ' ' + last_name
    else:
        full_name = first_name + ' ' + last_name
    return full_name.title()

musician = get_formatted_name('jimi', 'hendrix')
print(musician)

musician = get_formatted_name('john', 'hooker', 'lee')
print(musician)
```
在函数体中，我们检查是否提供了中间名。Python将非空字符串解读为True。
可选值让函数能够处理各种不同情形的同时，确保函数调用尽可能简单。

### 8.3.3 返回字典
函数可返回任何类型的值，包括列表和字典等较复杂的数据结构。例如，下面的函数接受姓名的组成部分，并返回一个表示人的字典：
```Python
def build_person(first_name, last_name):
    """返回一个字典，其中包含有关一个人的信息"""
    person = {'first': first_name, 'last': last_name}
    return person

musician = build_person('jimi', 'hendrix')
print(musician)
```
这个函数接受简单的文本信息，将其放在一个更合适的数据结构中，让你不仅能打印这些信息，还能以其他方式处理它们。当前，字符串'jimi'和'hendrix'被标记为名和姓。你可以轻松地扩展这个函数，使其接受可选值，如中间名、年龄、职业或你要存储的其他任何信息。例如，下面的修改让你还能存储年龄：
```Python
def build_person(first_name, last_name, age=''):
    """返回一个字典，其中包含有关一个人的信息"""
    person = {'first': fist_name, 'last': last_name}
    if age:
        person['age'] = age
    return person

musician = build_person('jimi', 'hendrix', age = 27)
print(musician)
```
在函数定义中，我们新增了一个可选形参age，并将其默认值设置为空字符串。如果函数调用中包含这个形参的值，这个值将存储到字典中。在任何情况下，这个函数都会存储人的姓名，但可对其进行修改，使其也存储有关人的其他信息。

### 8.3.4 结合使用函数和while循环
可将函数同本书前面介绍的任何Python结构结合起来使用。例如，下面将结合使用函数get_formatted_name()和while循环，以更正规的方式问候用户。下面尝试使用名和姓跟用户打招呼：
```Python
def get_formatted_name(first_name, last_name):
    """返回整洁的姓名"""
    full_name = first_name + ' ' + last_name
    return full_name.title()

# 这是一个无限循环!
while True:
    print("\nPlease tell me you name:")
    f_name = input("First name: ")
    l_name = input("Last name: ")

formatted_name = get_formatted_name(f_name, l_name)
print("\nHello, " + formatted_name + "!")
```
在这个示例中，我们使用的是get_formatted_name()的简单版本，不涉及中间名。其中的while循环让用户输入姓名：依次提示用户输入名和姓。

但这个while循环存在一个问题：没有定义退出条件。请用户提供一系列输入时，该在什么地方提供退出条件呢？我们要让用户能够尽可能容易地退出，因此每次提示用户输入时，都应提供退出途径。每次提示用户输入时，都使用break语句提供了退出循环的简单途径：
```Python
def get_formatted_name(first_name, last_name):
    """返回整洁的姓名"""
    full_name = first_name + ' ' + last_name
    return full_name.title()

while True:
    print("\nPlease tell me your name:")
    print("(enter 'q' at any time to quit)")

    f_name = input("First name: ")
    if f_name == 'q':
        break

    l_name = input("Last name: ")
    if l_name == 'q':
        break
    
    formatted_name = get_formatted_name(f_name, l_name)
    print("\nHello, " + formatted_name + "!")
```
我们添加了一条消息来告诉用户如何退出，然后再每次提示用户输入时，都检查他输入的是否是退出值，如果是，就退出循环。现在，这个程序将不断地问候，直到用户输入的姓或名为'q'为止。

## 8.4 传递列表
你经常会发现，向函数传递列表很有用，这种列表包含的可能是名字、数字或更复杂的对象(如字典)。将列表传递给函数后，函数就能直接访问其内容。下面使用函数来提高处理列表的效率。

假设有一个用户列表，我们要问候其中的每位用户。下面的示例将一个名字列表传递给一个名为greet_users()的函数，这个函数问候列表中的每个人：
```Python
def greet_users(names):
    """向列表中的每位用户都发出简单的问候"""
    for name in names:
        msg = "Hello, " + name.title() + "!"
        print(msg)

usernames = ['hannah', 'ty', 'margot']
greet_users(usernames)
```

### 8.4.1 在函数中修改列表
将列表传递给函数后，函数就可对其进行修改。在函数中对这个列表所做的任何修改都是永久性的，这让你能够高效地处理大量的数据。

来看一家为用户提交的设计制作3D打印模型的公司。需要打印的设计存储在一个列表中，打印后移动到另一个列表中。下面是在不使用函数的情况下模拟这个过程的代码：
```Python
# 首先创建一个列表，其中包含一些要打印的设计
unprinted_designs = ['iphone case', 'robot pendant', 'dodecahedron']
completed_models = []

# 模拟打印每个设计，直到没有未打印的设计为止
# 打印每个设计后，都将其移动到列表completed_models中
while unprinted_designs:
    current_design = unprinted_designs.pop()

    # 模拟根据设计制作3D打印模型的过程
    print("Printing model: " + current_design)
    completed_models.append(current_design)

# 显示打印好的所有模型
print("\nThe following models have been printed:")
for completed_model in completed_models:
    print(completed_model)
```
为重新组织这些代码，我们可编写两个函数，每个都做一件具体的工作。大部分代码都与原来相同，只是效率更高。第一个函数将负责处理打印设计的工作，而第二个将概述打印了那些设计：
```Python
def print_models(unprinted_designs, completed_models):
    """
    模拟打印每个设计，直到没有未打印的设计为止
    打印每个设计后，都将其移动到列表completed_models中
    """
    while unprinted_designs:
        current_design = unprinted_designs.pop()

        # 模拟根据设计制作3D打印模型的过程
        print("Printing model: " + current_design)
        completed_models.append(current_design)

def show_complete_models(completed_models):
    """显示打印好的所有模型"""
    print("\nThe following models have been printed:")
    for completed_model in completed_models:
        print(completed_model)

unprinted_designs = ["iphone case', 'robot pendant', 'dodecahedron']
completed_models = []

print_models(unprinted_designs, completed_models)
show_completed_models(completed_models)
```
相比于没有使用函数的版本，这个程序更容易扩展和维护。如果以后需要打印其他设计，只需要再次调用print_models()即可。如果我们发现需要对打印代码进行修改，只需修改这些代码一次，就能影响所有调用该函数的地方；与必须分别修改程序的多个地方相比，这种修改的效率更高。

这个程序还演示了这样一种理念，即每个函数都应只负责一项具体的工作。第一个函数打印每个设计，而第二个显示打印好的模型；这优于使用一个函数来完成两项工作。编写函数时，如果你发现它执行的任务太多，请尝试将这些代码划分到两个函数中。别忘了，总是可以在一个函数中调用另一个函数，这又助于将复杂任务划分为一系列的步骤。

### 8.4.2 禁止函数修改列表
有时候，需要禁止函数修改列表。例如，假设像前一个示例那样，你有一个未打印的设计列表，并编写了一个将这些设计移到打印好的模型列表中的函数。你可能会做出这样的决定：即便打印所有设计后，也要保留原来的未打印的设计列表，以供备案。但由于你将所有的设计都移出了unprinted_designs,这个列表变成了空的，原来的列表没有了。为解决这个问题，可向函数传递列表的的副本而不是原件；这样函数所做的任何修改都只影响副本，而丝毫不影响原件。
要将列表的副本传递给函数，可以像下面这样做：
```Python
function_name(list_name[:])
```
切片表示法[:]创建列表的副本。在print_models中，如果不想清空未打印的设计列表，可像下面这样调用print_models():
```Python
print_models(unprinted_designs[:], completed_models)
```
这样函数print_models()依然能够完成其工作，因为它获得了所有未打印的设计的名称，但它使用的是列表unprinted_designs的副本，而不是列表unprinted_designs本身。像以前一样，列表completed_models也将包含打印好的模型的名称，但函数所做的修改不会影响到列表unprinted_designs。

## 8.5 传递任意数量的实参
有时候，你预先不知道函数需要接受多少个实参，好在Python允许函数从调用语句中收集任意数量的实参。

例如，来看一个制作比萨的函数，它需要接受很多配料，但你无法预先确定顾客要多少种配料。下面的函数只有一个形参*toppings,但不管调用语句提供了多少实参，这个形参都将它们统统收入囊中：
```Python
def make_pizza(*toppings):
    """打印顾客点的所有配料"""
    print(toppings)

make_pizza('pepperoni')
make_pizza('mushrooms', 'green peppers', 'extra cheese')
```
形参名*toppings中的**星号**让python创建一个名为toppings的**空元组**，并将收到的所有值都封装到这个元组中。函数体内的print语句通过生成输出来证明Python能够处理使用一个值调用函数的情形，也能处理使用三个值来调用函数的情形。它以类似的方式处理不同的调用，注意，Python将实参封装到一个元组中，即便函数只收到一个值也如此。

现在，我们可以将这条print语句替换为一个循环，对配料列表进行遍历，并对顾客点的比萨进行描述：
```Python
def make_pizza(*toppings):
    """概述要制作的比萨"""
    print("\nMaking a pizza with the following toppings:")
    for topping in toppings:
        print("- " + topping)

make_pizza('pepperoni')
make_pizza('mushrooms', 'green peppers', 'extra cheese')
```
不管收到的是一个值还是三个值，这个函数都能妥善地处理：
不管函数收到地实参是多少个，这种语法都管用。

### 8.5.1 结合使用位置实参和任意数量实参
如果要让函数接受不同类型的实参，必须在函数定义中将接纳任意数量实参的形参放在最后。Python先匹配位置实参和关键字实参，再将余下的实参都收集到最后一个形参中。
例如，如果前面的函数还需要一个表示比萨尺寸的实参，必须将该形参放在形参*toppings的前面：
```Python
def make_pizza(size, *toppings):
    """概述要制作的比萨"""
    print("\nMaking a " + str(size) + "-inch pizza with the following toppings:")
    for topping in toppings:
        print("- " + topping)

make_pizza(16, 'pepperoni')
make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
```
基于上述函数定义，Python将收到的第一个值存在形参size中，并将其他的所有值都存储在元组toppings中。在函数调用中，首先指定比萨尺寸的实参，然后根据需要指定任意数量的配料。
现在，每个比萨都有了尺寸和一系列配料，这些信息按正确的顺序打印出来了————首先是尺寸，然后是配料。

### 8.5.2 使用任意数量的关键字实参
有时候，需要接受任意数量的实参，但预先不知道传递给函数的会是什么样的信息。在这种情况下，可将函数编写成能够接受任意数量的键——值对————调用语句提供了多少就接受多少。一个这样的示例是创建用户简介：你知道你将收到有关用户的信息，但不确定会是什么样的信息。在下面的示例中，函数build_profile()接受名和姓，同时还接受任意数量的关键字实参：
```Python
def build_profile(first, last, **user_info):
    """创建一个字典，其中包含我们知道的有关用户的一切"""
    profile = {}

    profile['first_name'] = first
    profile['last_name'] = last
    for key, value in user_info.items():
        profile[key] = value
    return profile

user_profile = build_profile('albert', 'einstein',
                            location = 'princeton',
                            field = 'physics')
print(user_profile)
```
函数build_profile()的定义要求提供名和姓，同时允许用户根据需要提供任意数量的名称——值对。形参**user_info中的两个星号让Python创建一个名为user_info的空字典，并将收到的所有名称——值对都封装到这个字典中。在这个函数中，可以像访问其他字典那样访问User_info中的名称——值对。

编写函数时，你可以以各种方式混合使用位置实参、关键字实参和任意数量的实参。知道这些实参类型大有裨益，以为阅读别人编写的代码时经常会见到它们。要正确地使用这些类型的实参并知道它们的使用时机，需要经过一定的练习。就目前而言，牢记使用最简单的方法来完成任务就好了。你继续往下阅读，就会知道在各种情况下哪种方法的效率是最高的。

## 8.6 将函数存储在模块中
函数的优点之一是，使用它们可将代码块与主程序分离。通过函数指定描述性名称，可让主程序容易理解得多。你还可以更进一步，将函数存储在被称为**模块**的独立文件中，再将模块**导入**到主程序中。import语句允许在当前运行的程序文件中使用模块中的代码。

通过将函数存储在独立的文件中，可隐藏程序代码的细节，将重点放在程序的高层逻辑上。这还能让你在众多不同的程序中重用函数。将函数存储在独立文件中后，可与其他程序导入模块的方法有多种，下面对每种都作简要的介绍。

### 8.6.1 导入整个模块
要让函数是可导入的，得先**创建模块**。**模块**是扩展名为.py的文件，包含要导入到程序中的代码。下面来创建一个包含函数make_pizza()的模块。为此，我们将文件pizza.py中除函数make_pizza()之外的其它代码都删除：
```Python
# pizza.py
def make_pizza(size, *toppings):
    """概述要制作的比萨"""
    print("\nMaking a " + str(size)) + "-inch pizza with the following toppings:")
    for topping in toppings:
        print("- " + topping)
```
接下来，我们在pizza.py所在的目录中创建另一个为making_pizzas.py的文件，这个文件导入刚创建的模块，再调用make_pizza()两次：
```Python
# making_pizzas.py
import pizza

pizza.make_pizza(16, 'pepperoni')
pizza.make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
```
Python读取这个文件时，代码行import pizza让Python打开文件pizza.py，并将其中的所有函数都复制到这个程序中。你看不到复制的代码，因为这个程序运行时，Python在幕后复制这些代码。你只需知道，在making_pizzas.py中，可以使用pizza.py中定义的所有函数。

要调用被导入的模块中的函数，可指定导入的模块的名称pizza和函数名make_pizza()，并用句点分隔它们。这些代码的输出与没有导入模块的原始程序相同。

这就是一种导入方法：只需编写一条import语句并在其中指定模块名，就可在程序中使用该模块中的所有函数。如果你使用这种import语句导入了名为module_name.py的整个模块，就可使用下面的语法来使用其中任何一个函数：
```Python
moudle_name.function_name()
```
### 8.6.2 导入特定的函数
你还可以导入模块中的特定函数，这种导入方法的语法如下：
```python
from moudle_name import function_name
```
通过用逗号分隔函数名，可根据需要从模块中导入任意数量的函数：
```Python
from moudle_name import function_0, function_1, function_2
```
对于前面的making_pizzas.py示例，如果只想导入要使用的函数，代码将类似于下面这样:
```Python
from pizza import make_pizza

make_pizza(16, 'pepperoni')
make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
```
若使用这种语法，调用函数时就无需使用句点。由于我们在import语句中显示地导入了函数make_pizza(),因此调用它时只需要指定其名称。

### 8.6.3 使用as给函数指定别名
如果要导入的函数的名称可能与程序中现有的名称冲突，或者函数的名称太长，可指定简短而独一无二的别名————函数的另一个名称，类似于外号。要给函数指定这种特殊外号，需要在导入它时这样做。
下面给函数make_pizza()指定了别名mp()。这是在import语句中使用make_pizza as mp实现的，关键字as将函数重命名为你提供的别名：
```Python
from pizza import make_pizza as mp

mp(16, 'pepperoni')
mp(12, 'mushrooms', 'green peppers', 'extra cheese')
```
上面的import语句将函数make_pizza()重命名mp()；在这个程序中，每当需要调用make_pizza()时，都可简写成mp()，而Python将运行make_pizza()中的代码，这可避免与这个程序可能包含的函数make_pizza()混淆。

指定别名的通用语法如下：
```Python
from moudle_name import function_name as fn
```

### 8.6.4 使用as给模块指定别名
你还可以给模块指定别名。通过给模块指定简短的别名(如给模块pizza指定别名p)，让你能够更轻松地调用模块中的函数。相比于pizza.make_pizza()，p.make_pizza()更为简洁:
```Python
import pizza as p

p.make_pizza(16, 'pepperoni')
p.make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
```
上述import语句给模块pizza指定了别名p，但该模块中所有函数的名称都没变。调用函数make_pizza()时，可编写代码p.make_pizza()而不是pizza.make_pizza()，这样不仅能使代码更简介，还可以让你不再关注模块名，而专注于描述性的函数名。这些函数名明确地指出了函数的功能，对理解代码而言，它们比模块名更重要。
给模块指定别名的通用语法如下：
```Python
import module_name as mn
```

### 8.6.5 导入模块中的所有函数
使用星号(*)运算符可让Python导入模块中的所有函数：
```Python
from pizza import *

make_pizza(16, 'pepperoni')
make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
```
import语句中的星号让Python将模块pizza中的每个函数都复制到这个程序文件中。由于导入了每个函数，可通过名称来调用每个函数，而无需使用句点表示法。然而，使用并非自己编写的大型模块时，最好不要采用这种导入方法：如果模块中有函数的名称与你的项目中使用的名称相同，可能导致意想不到的结果：Python可能遇到多个名称相同的函数或变量，进而覆盖函数，而不是分别导入所有的函数。

最佳的做法是，要么只导入你需要使用的函数，要么导入整个模块并使用句点表示法。这能让代码更清晰，更容易阅读和理解。这里之所以介绍这种导入方法，只是想让你在阅读别人编写的代码时，如果遇到类似于下面的import语句，能够理解它们：
```Python
from module_name import *
```
## 8.7 函数编写指南
编写函数时，需要牢记几个细节。应给函数指定描述性名称，且只在其中使用小写字母和下划线。描述性名称可帮助你和别人明白代码想要做什么。给模块命名时也应遵循上述约定。
每个函数都应包含简要地阐述其功能的注释，该注释应紧跟在函数定义后面，并采用文档字符串格式。文档良好的函数让其他程序员只需阅读文档字符串中的描述就能够使用它：它们完全可以相信代码如描述的那样运行；只要知道函数的名称、需要的实参以及返回值的类型，就能在自己的程序中使用它。
给形参指定默认值时，等号两边不要有空格：
```Python
def function_name(parameter_0, parameter_1='default value')
```
对于函数调用中的关键字实参，也应遵循这种约定：
```Python
function_name(value_0, parameter_1='value')
```
[PEP 8](https://www.python.org/dev/peps/pep-0008)建议代码行的长度不要超过79字符，这样只要编辑器窗口适中，就能看到整行代码。如果形参很多，导致函数定义的长度超过了79字符，可在函数定义中输入左括号后按回车键，并在下一行按两次Tab键，从而将形参列表和只缩进一层的函数体区分开来。

大多数编辑器都会自动对齐后续参数列表行，使其缩进程度与你给第一个参数列表行指定的缩进程度相同：
```Python
def function_name(
        parameter_0, parameter_1, parameter_2,
        parameter_3, parameter_4, parameter_5):
    function body……
```
如果程序或模块包含多个函数，可使用两个空行将相邻的函数分开，这样将更容易知道前一个函数在什么地方结束，下一个函数从什么地方开始。
所有的import语句都应放在文件开头，唯一例外的情形是，在文件开头使用了注释来描述整个程序。

# 第9章 类
**面向对象编程**是最有效的软件编写方法之一。在面向对象编程中，你编写表示现实世界中的事物和情景的类，并基于这些类来创建对象。编写类时，你定义一大类对象都有的通用行为。基于类创建**对象**时，每个对象都自动具备这种通用行为，然后可根据需要赋予每个对象独特的个性。使用面向对象编程可模拟现实情景，其逼真程度达到了令你惊讶的地步。

根据类来创建对象被称为**实例化**，这让你能够使用类的实例。在本章中，你将编写一些类并创建其实例。你将指定可在实例中存储什么信息，定义可对这些实例执行哪些操作。你还将编写一些类来扩展既有类的功能，让相似的类能够高效地共享代码。你将把自己编写的类存储在模块中，并在自己的程序文件中导入其他程序员编写的类。

理解面向对象编程有助于你像程序员那样看世界，还可以帮助你真正明白自己编写的代码：不仅是各行代码的作用，还有代码背后更宏大的概念。了解类背后的概念可培养逻辑思维，让你能够通过编写程序来解决遇到的几乎任何问题。

随着面临的挑战日益严峻，类还能让你以及与你合作的其他程序员的生活更轻松。如果你与其他程序员基于同样的逻辑来编写代码，你们就能明白对方所做的工作；你编写的程序将能被众多合作者所理解，每个人都能事半功倍。
## 9.1 创建和使用类
使用类几乎可以模拟任何东西。下面来编写一个表示小狗的简单类Dog——它表示的不是特定的小狗，而是任何小狗。对于大多数宠物狗，我们都知道些什么呢？它们都有名字和年龄；我们还知道，大多数小狗还会蹲下和打滚。由于大多数小狗都具备上述两项信息(名字和年龄)和两种行为(蹲下和打滚)，我们的Dog类将包含它们。这个类让Python知道如何创建表示小狗的对象。编写这个类后，我们将使用它来创建表示特定小狗的实例。

### 9.1.1 创建Dog类
根据Dog类创建的每个实例都将存储名字和年龄。我们赋予了每条小狗蹲下(sit())和打滚(roll_over())的能力：
```Python
class Dog():
    """一次模拟小狗的简单尝试"""

    def __init__(self, name, age):
        """初始化属性name和age"""
        self.name = name
        self.age = age

    def sit(self):
        """模拟小狗被命令时蹲下"""
        print(self.name.title() + " is now sitting.")

    def roll_over(self):
        """模拟小狗被命令时打滚"""
        print(self.name.title() + " rolled over!")
```
这里需要注意的地方很多，但你也不用担心，本章充斥着这样的结果，你有大把的机会熟悉它。我们定义了一个名为Dog的类。根据约定，在Python中，首字母大写的名称指的是类。这个类定义中的括号是空的，因为我们要从空白创建这个类。我们编写了一个文档字符串，对这个类的功能作了描述。

1. 方法__init__()
类中函数称为方法；你前面学到的有关函数的一切都适用与方法，就目前而言，唯一重要的差别是调用方法的方式。方法__init__()是一个特殊的方法，每当你根据Dog类创建新实例时，Python都会自动运行它。在这个方法的名称中，开头和末尾各有两个下划线，这是一种约定，旨在避免Python默认方法与普通方法发生名称冲突。

我们将方法__init__()定义成了包含三个形参：self、name和age。在这个方法的定义中，形参self必不可少，还必须位于其他形参的前面。为何必须在方法定义中包含形参self呢？因为Python调用这个__init__()方法来创建Dog实例时，将自动传入实参self。每个与类相关联的方法调用都自动传递实参self，它是一个指向实例本身的引用，让实例能够访问类中的属性和方法。我们创建Dog实例时，Python将调用Dog类的方法__init__()。我们将通过实参向Dog()传递名字和年龄；self会自动传递，因此我们不需要传递它。每当我们根据Dog类创建实例时，都只需给最后两个形参(name和age)提供值。

self.name……处定义的两个变量都有前缀self。以self为前缀的变量都可供类中的所有方法使用，我们还可以通过类的任何实例来访问这些变量。self.name = name获取存储在形参name中的值，并将其存储到变量name中，然后该变量被关联到当前创建的实例。self.age = age的作用与此类似。像这样可通过实例访问的变量称为**属性**。

Dog类还定义了另外两个方法：sit()和roll_over()。由于这些方法不需要额外的信息，如名字或年龄，因此它们只有一个形参self。我们后面将创建的实例能够访问这些方法，换句话说，它们都会蹲下和打滚。当前，sit()和roll_over()所做的有限，它们只是打印一条消息，指出小狗正蹲下或打滚。但可以扩展这些方法以模拟实际情况：如果这个类包含在一个计算机游戏中，这些方法将包含创建小狗蹲下和打滚动画效果的代码。如果这个类是用于控制机器狗的，这些方法将引导机器狗做出蹲下和打滚的动作。

2. 在Python2.7中创建类

在Python2.7中创建类时，需要做细微的修改————在括号内包含单词object：
```Python
class ClassName(object):
    --snip--
```
这让Python2.7类的行为更像Python3类，从而简化了你的工作。

在Python2.7中定义Dog类时，代码类似于下面这样：
```Python
class Dog(object):
    --snip--
```

### 9.1.2 根据类创建实例
可将类视为有关如何创建实例的说明。Dog类是一系列的说明，让Python知道如何创建表示特定小狗的实例。
下面来创建一个表示特定小狗的实例:
```Python
class Dog():
    --snip--

my_dog = Dog('willie', 6)

print("My dog's name is " + my_dog.name.title() + ".")
print("My dog is " + str(my_dog.age) + " years old.")
```
在这里，命名约定很有用：我们通常可以认为首字母大写的名称(如Dog)指的是类，而小写的名称(如my_dog)指的是根据类创建的实例。

1. 访问属性

要访问实例的属性，可使用句点表示法。我们编写了如下代码来访问my_dog的属性name的值：
```Python
my_dog.name
```
句点表示法在Python中很常用，这种语法演示了Python如何获悉属性的值。在这里，Python先找到实例my_dog，再查找与这个实例相关联的属性。在Dog类中引用这个属性时，使用的是self.name。

2. 调用方法
   ……


## 9.2 使用类和实例
你可以使用类来模拟现实世界中的很多情景。类编写好后，你的大部分时间都将花费在使用根据类创建的实例上。你需要执行的一个重要任务是修改实例的属性。你可以直接修改实例的属性，也可以编写方法以特定的方式进行修改。

### 9.2.1 Car类
下面来编写一个表示汽车的类，它存储了有关汽车的信息，还有一个汇总这些信息的方法：
```Python
class Car():
    """一次模拟汽车的简单尝试"""

    def __init__(self, make, model, year):
        """初始化描述汽车的属性"""
        self.make = make
        self.model = model
        self.year = year
    
    def get_descriptive_name(self):
        """返回整洁的描述性信息"""
        long_name = str(self.year) + ' ' + self.make + ' ' + self.model
        return long_name.title()

my_new_car = Car('audi', 'a4', 2016)
print(my_new_car.get_descriptive_name())
```
我们调用方法get_descriptive_name()，指出我们拥有的是一辆什么样的汽车，为让这个类更有趣，下面给它添加一个随时间变化的属性，它存储汽车的总里程。

### 9.2.2 给属性指定默认值
类中的每个属性都必须有初始值，哪怕这个值是0或空字符串。在有些情况下，如设置默认值时，在方法__init__()内指定这种初始值是可行的；如果你对某个属性这样做了，就无需包含为它提供初始值的形参。

下面来添加一个名为odometer_reading的属性，其初始值总是为0。我们还添加了一个名为read_odometer()的方法，用于读取汽车的历程表：
```Python
class Car():

    def __init__(self, make, model, year):
        """初始化描述汽车的属性"""
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
    
    def get_descriptive_name(self):
        --snip--
    
    def read_odometer(self):
        """打印一条指出汽车历程的消息"""
        print("This car has " + str(self.odometer_reading) + " miles on it.")

my_new_car = Car('audi', 'a4', 2016)
print(my_new_car.get_descriptive_name())
my_new_car.read_odometer()
```
现在，当Python调用方法__init__()来创建新实例时，将像前一个实例一样以属性的方式存储制造商、型号和生产年份。接下来，Python将创建一个名为odometer_reading的属性，并将其初始值设置为0。在read_odometer处，我们还定义了一个名为read_odormeter()的方法，它让你能够轻松地获悉汽车的里程。

一开始汽车的里程为0。出售时里程表读数为0的汽车并不多，因此我们需要一个修改该属性的值的途径。

### 9.2.2 修改属性的值
可以以三种不同的方式修改属性的值：直接通过实例进行修改；通过方法进行设置；通过方法进行递增(增加特定的值)。下面依次介绍这些方法。

1. 直接修改属性的值
要修改属性的值，最简单的方式是通过实例直接访问它。下面的代码直接将里程表读数设置为23：
```Python
class Car():
    --snip--

my_new_car = Car('audi','a4', 2016)
print(my_new_car.get_descriptive_name())

my_new_car.odometer_reading = 23
my_new_car.read_odometer()
```
