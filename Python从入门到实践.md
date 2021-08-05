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
