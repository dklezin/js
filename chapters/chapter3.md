#Функции

<i>Люди считают, что компьютерные науки – это искусство для гениев. В реальности всё наоборот – просто множество людей делают вещи, которые стоят друг на друге, будто составляя стену из маленьких камушков.

Дональд Кнут</i>

Вы уже видели вызовы функций, таких как `alert`. Функции – это хлеб с маслом программирования на JavaScript. Идея оборачивания куска программы и вызова её как переменной очень востребована. Это инструмент для структурирования больших программ, уменьшения повторений, назначения имён подпрограммам, и изолирование подпрограмм друг от друга.

Самое очевидное использование функций – создание нового словаря. Придумывать слова для обычной человеческой прозы – дурной тон. В языке программирования это необходимо.

Средний взрослый русскоговорящий человек знает примерно 10000 слов. Редкий язык программирования содержит 10000 встроенных команд. И словарь языка программирования определён чётче, поэтому он менее гибок, чем человеческий. Поэтому нам обычно приходится добавлять в него свои слова, чтобы избежать излишних повторений.

##Определение функции
Определение функции – обычное определение переменной, где значение, которое получает переменная, является функцией. Например, следующий код определяет переменную square, которая ссылается на функцию, подсчитывающую квадрат заданного числа:

```
var square = function(x) {
  return x * x;
};

console.log(square(12));
// → 144
```

Функция создаётся выражением, начинающимся с ключевого слова `function`. У функций есть набор параметров (в данном случае, только x), и тело, содержащее инструкции, которые необходимо выполнить при вызове функции. Тело функции всегда заключают в фигурные скобки, даже если оно состоит из одной инструкции.

У функции может быть несколько параметров, или вообще их не быть. В следующем примере makeNoise не имеет списка параметров, а у power их целых два:

```
var makeNoise = function() {
  console.log("Хрясь!");
};

makeNoise();
// → Хрясь!

var power = function(base, exponent) {
  var result = 1;
  for (var count = 0; count < exponent; count++)
    result *= base;
  return result;
};

console.log(power(2, 10));
// → 1024
```

Некоторые функции возвращают значение, как power и square, другие не возвращают, как makeNoise, которая производит только побочный эффект. Инструкция `return` определяет значение, возвращаемое функцией. Когда обработка программы доходит до этой инструкции, она сразу же выходит из функции, и возвращает это значение в то место кода, откуда была вызвана функция. return без выражения возвращает значение `undefined`.

##Параметры и область видимости
Параметры функции – такие же переменные, но их начальные значения задаются при вызове функции, а не в её коде.

Важное свойство функций в том, что переменные, созданные внутри функции (включая параметры), локальны внутри этой функции. Это означает, что в примере с power переменная result будет создаваться каждый раз при вызове функции, и эти отдельные её инкарнации никак друг с другом не связаны.

Эта локальность переменных применяется только к параметрам и созданным внутри функций переменным. Переменные, заданные снаружи какой бы то ни было функции, называются глобальными, поскольку они видны на протяжении всей программы. Получить доступ к таким переменным можно и внутри функции, если только вы не объявили локальную переменную с тем же именем.

Следующий код иллюстрирует это. Он определяет и вызывает две функции, которые присваивают значение переменной x. Первая объявляет её как локальную, тем самым меняя только локальную переменную. Вторая не объявляет, поэтому работа с x внутри функции относится к глобальной переменной x, заданной в начале примера.

```
var x = "outside";

var f1 = function() {
  var x = "inside f1";
};
f1();
console.log(x);
// → outside

var f2 = function() {
  x = "inside f2";
};
f2();
console.log(x);
// → inside f2
```

Такое поведение помогает предотвратить случайное взаимодействие между функциями. Если бы все переменные использовались в любом месте программы, было бы очень трудно убедиться, что одна переменная не используется по разным назначениям. А если бы вы использовали переменную повторно, вы бы столкнулись со странными эффектами, когда сторонний код портит значения вашей переменной. Относясь к локальным для функций переменным так, что они существуют только внутри функции, язык делает возможным работу с функциями будто с отдельными маленькими вселенными, что позволяет не волноваться про весь код целиком.

##Вложенные области видимости
JavaScript различает не только глобальные и локальные переменные. Функции можно задавать внутри функций, что приводит к нескольким уровням локальности.

К примеру, следующая довольно бессмысленная функция содержит внутри ещё две:

```
var landscape = function() {
  var result = "";
  var flat = function(size) {
    for (var count = 0; count < size; count++)
      result += "_";
  };
  var mountain = function(size) {
    result += "/";
    for (var count = 0; count < size; count++)
      result += "'";
    result += "\\";
  };

  flat(3);
  mountain(4);
  flat(6);
  mountain(1);
  flat(1);
  return result;
};

console.log(landscape());
// → ___/''''\______/'\_
```

Функции flat и mountain видят переменную result, потому что они находятся внутри функции, в которой она определена. Но они не могут видеть переменные count друг друга, потому что переменные одной функции находятся вне области видимости другой. А окружение снаружи функции landscape не видит ни одной из переменных, определённых внутри этой функции.

Короче говоря, в каждой локальной области видимости можно увидеть все области, которые её содержат. Набор переменных, доступных внутри функции, определяется местом, где эта функция описана в программе. Все переменные из блоков, окружающих определение функции, видны – включая и те, что определены на верхнем уровне в основной программе. Этот подход к областям видимости называется лексическим.

Люди, изучавшие другие языки программирования, могут подумать, что любой блок, заключённый в фигурные скобки, создаёт своё локальное окружение. Но в JavaScript область видимости создают только функции. Вы можете использовать отдельно стоящие блоки:

```
var something = 1;
{
  var something = 2;
  // Делаем что-либо с переменной something...
}
// Вышли из блока...
```

Но something внутри блока – это та же переменная, что и снаружи. Хотя такие блоки и разрешены, имеет смысл использовать их только для команды if и циклов.

Если это кажется вам странным – так кажется не только вам. В версии JavaScript 1.7 появилось ключевое слово let, которое работает как var, но создаёт переменные, локальные для любого данного блока, а не только для функции.

##Функции как значения
Имена функций обычно используют как имя для кусочка программы. Такая переменная однажды задаётся и не меняется. Так что легко перепутать функцию и её имя.

Но это – две разные вещи. Вызов функции можно использовать, как простую переменную – например, использовать их в любых выражениях. Возможно хранить вызов функции в новой переменной, передавать её как параметр другой функции, и так далее. Также переменная, хранящая вызов функции, остаётся обычной переменной и её значение можно поменять:

```
var launchMissiles = function(value) {
  missileSystem.launch("пли!");
};
if (safeMode)
  launchMissiles = function(value) {/* отбой */};
```

В главе 5 мы обсудим чудесные вещи, которые возможно сделать, передавая вызовы функций другим функциям.

##Объявление функций
Есть более короткая версия выражения “var square = function…”. Ключевое слово function можно использовать в начале инструкции:

```
function square(x) {
  return x * x;
}
```

Это объявление функции. Инструкция определяет переменную square и присваивает ей заданную функцию. Пока всё ок. Есть только один подводный камень в таком определении.

```
console.log("The future says:", future());

function future() {
  return "We STILL have no flying cars.";
}
```

Такой код работает, хотя функция объявляется ниже того кода, который её использует. Это происходит оттого, что объявления функций не являются частью обычного исполнения программ сверху вниз. Они «перемещаются» наверх их области видимости и могут быть вызваны в любом коде в этой области. Иногда это удобно, потому что вы можете писать код в таком порядке, который выглядит наиболее осмысленно, не беспокоясь по поводу необходимости определять все функции выше того места, где они используются.

А что будет, если мы поместим объявление функции внутрь условного блока или цикла? Не надо так делать. Исторически разные платформы для запуска JavaScript обрабатывали такие случаи по разному, а текущий стандарт языка запрещает так делать. Если вы хотите, чтобы ваши программы работали последовательно, используйте объявления функций только внутри других функций или основной программы.

```
function example() {
  function a() {} // Нормуль
  if (something) {
    function b() {} // Ай-яй-яй!
  }
}
```

###Стек вызовов
Полезным будет присмотреться к тому, как порядок выполнения работает с функциями. Вот простая программа с несколькими вызовами функций:

```
function greet(who) {
  console.log("Привет, " + who);
}
greet("Семён");
console.log("Покеда");
```

Обрабатывается она примерно так: вызов greet заставляет проход прыгнуть на начало функции. Он вызывает встроенную функцию console.log, которая перехватывает контроль, делает своё дело и возвращает контроль. Потом он доходит до конца greet, и возвращается к месту, откуда его вызвали. Следующая строчка опять вызывает console.log.

Схематично это можно показать так:

```
top
   greet
        console.log
   greet
top
   console.log
top
```

Поскольку функция должна вернуться на то место, откуда её вызвали, компьютер должен запомнить контекст, из которого была вызвана функция. В одном случае, console.log должна вернуться обратно в greet. В другом, она возвращается в конец программы.

Место, где компьютер запоминает контекст, называется стеком. Каждый раз при вызове функции, текущий контекст помещается наверх стека. Когда функция возвращается, она забирает верхний контекст из стека и использует его для продолжения работы.

Хранение стека требует места в памяти. Когда стек слишком сильно разрастается, компьютер прекращает выполнение и выдаёт что-то вроде “stack overflow” или “ too much recursion”. Следующий код это демонстрирует – он задаёт компьютеру очень сложный вопрос, который приводит к бесконечным прыжкам между двумя функциями. Точнее, это были бы бесконечные прыжки, если бы у компьютера был бесконечный стек. В реальности стек переполняется.

```
function chicken() {
  return egg();
}
function egg() {
  return chicken();
}
console.log(chicken() + " came first.");
// → ??
```

###Необязательные аргументы
Следующий код вполне разрешён и выполняется без проблем:

```
alert("Здрасьте", "Добрый вечер", "Всем привет!");
```

Официально функция принимает один аргумент. Однако, при таком вызове она не жалуется. Она игнорирует остальные аргументы и показывает «Здрасьте».

JavaScript очень лоялен по поводу количества аргументов, передаваемых функции. Если вы передадите слишком много, лишние будут проигнорированы. Слишком мало – отсутствующим будет назначено значение undefined.

Минус этого подхода в том, что возможно,- и даже вероятно,- передать функции неправильное количество аргументов, и вам никто на это не пожалуется.

Плюс в том, что вы можете создавать функции, принимающие необязательные аргументы. К примеру, в следующей версии функции power её можно вызывать как с двумя, так и с одним аргументом,- в последнем случае экспонента будет равна двум, и функция работает как квадрат.

```
function power(base, exponent) {
  if (exponent == undefined)
    exponent = 2;
  var result = 1;
  for (var count = 0; count < exponent; count++)
    result *= base;
  return result;
}

console.log(power(4));
// → 16
console.log(power(4, 3));
// → 64
```

В следующей главе мы увидим, как в теле функции можно узнать точное число переданных ей аргументов. Это полезно, т.к. позволяет создавать функцию, принимающую любое количество аргументов. К примеру, console.log использует это свойство, и выводит все переданные ему аргументы:

```
console.log("R", 2, "D", 2);
// → R 2 D 2
```

##Замыкания
Возможность использовать вызовы функций как переменные вкупе с тем фактом, что локальные переменные каждый раз при вызове функции создаются заново, приводит нас к интересному вопросу. Что происходит с локальными переменными, когда функция перестаёт работать?

Следующий пример иллюстрирует этот вопрос. В нём объявляется функция wrapValue, которая создаёт локальную переменную. Затем она возвращает функцию, которая читает эту локальную переменную и возвращает её значение.

```
function wrapValue(n) {
  var localVariable = n;
  return function() { return localVariable; };
}

var wrap1 = wrapValue(1);
var wrap2 = wrapValue(2);
console.log(wrap1());
// → 1
console.log(wrap2());
// → 2
```

Это допустимо и работает так, как должно – доступ к переменной остаётся. Более того, в одно и то же время могут существовать несколько экземпляров одной и той же переменной, что ещё раз подтверждает тот факт, что с каждым вызовом функции локальные переменные пересоздаются.

Эта возможность работать со ссылкой на какой-то экземпляр локальной переменной называется замыканием. Функция, замыкающая локальные переменные, называется замыкающей. Она не только освобождает вас от забот, связанных с временем жизни переменных, но и позволяет творчески использовать функции.

С небольшим изменением мы превращаем наш пример в функцию, умножающую числа на любое заданное число.

```
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

var twice = multiplier(2);
console.log(twice(5));
// → 10
```

Отдельная переменная вроде localVariable из примера с wrapValue уже не нужна. Так как параметр – сам по себе локальная переменная.

Потребуется практика, чтобы начать мыслить подобным образом. Хороший вариант мысленной модели – представлять, что функция замораживает код в своём теле и обёртывает его в упаковку. Когда вы видите return function(...) {...}, представляйте, что это пульт управления куском кода, замороженным для употребления позже.

В нашем примере multiplier возвращает замороженный кусок кода, который мы сохраняем в переменной twice. Последняя строка вызывает функцию, заключённую в переменной, в связи с чем активируется сохранённый код (return number * factor;). У него всё ещё есть доступ к переменной factor, которая определялась при вызове multiplier, к тому же у него есть доступ к аргументу, переданному во время разморозки (5) в качестве числового параметра.

##Рекурсия
Функция вполне может вызывать сама себя, если она заботится о том, чтобы не переполнить стек. Такая функция называется рекурсивной. Вот пример альтернативной реализации возведения в степень:

```
function power(base, exponent) {
  if (exponent == 0)
    return 1;
  else
    return base * power(base, exponent - 1);
}

console.log(power(2, 3));
// → 8
```

Примерно так математики определяют возведение в степень, и, возможно, это описывает концепцию более элегантно, чем цикл. Функция вызывает себя много раз с разными аргументами для достижения многократного умножения.

Однако, у такой реализации есть проблема – в обычной среде JavaScript она раз в 10 медленнее, чем версия с циклом. Проход по циклу выходит дешевле, чем вызов функции.

Дилемма «скорость против элегантности» довольно интересна. Есть некий промежуток между удобством для человека и удобством для машины. Любую программу можно ускорить, сделав её больше и замысловатее. От программиста требуется находить подходящий баланс.

В случае с первым возведением в степень, неэлегантный цикл довольно прост и понятен. Не имеет смысла заменять его рекурсией. Часто, однако, программы работают с такими сложными концепциями, что хочется уменьшить эффективность путём повышения читаемости.

Основное правило, которое уже не раз повторяли, и с которым я полностью согласен – не беспокойтесь насчёт быстродействия, пока вы точно не уверены, что программа тормозит. Если так, найдите те части, которые работают дольше всех, и меняйте там элегантность на эффективность.

Конечно, мы не должны сразу же полностью игнорировать быстродействие. Во многих случаях, как с возведением в степень, особой простоты от элегантных решений мы не получаем. Иногда опытный программист сразу видит, что простой подход никогда не будет достаточно быстрым.

Я заостряю на этом внимание оттого, что слишком много начинающих программистов хватаются за эффективность даже в мелочах. Результат получается больше, сложнее и часто не без ошибок. Такие программы дольше писать, а работают они часто не сильно быстрее.

Но рекурсия не всегда лишь менее эффективная альтернатива циклам. Некоторые задачи проще решить рекурсией. Чаще всего это обход нескольких веток дерева, каждая из которых может ветвиться.

Вот вам загадка: можно получить бесконечное количество чисел, начиная с числа 1, и потом либо добавляя 5, либо умножая на 3. Как нам написать функцию, которая, получив число, пытается найти последовательность таких сложений и умножений, которые приводят к заданному числу? К примеру, число 13 можно получить, сначала умножив 1 на 3, а затем добавив 5 два раза. А число 15 вообще нельзя так получить.

Рекурсивное решение:

```
function findSolution(target) {
  function find(start, history) {
    if (start == target)
      return history;
    else if (start > target)
      return null;
    else
      return find(start + 5, "(" + history + " + 5)") ||
             find(start * 3, "(" + history + " * 3)");
  }
  return find(1, "1");
}

console.log(findSolution(24));
// → (((1 * 3) + 5) * 3)
```

Этот пример не обязательно находит самое короткое решение – он удовлетворяется любым. Не ожидаю, что вы сразу поймёте, как программа работает. Но давайте разбираться в этом отличном упражнении на рекурсивное мышление.

Внутренняя функция find занимается рекурсией. Она принимает два аргумента – текущее число и строку, которая содержит запись того, как мы пришли к этому номеру. И возвращает либо строчку, показывающую нашу последовательность шагов, либо null.

Для этого функция выполняет одно из трёх действий. Если заданное число равно цели, то текущая история как раз и является способом её достижения, поэтому она и возвращается. Если заданное число больше цели, продолжать умножения и сложения смысла нет, потому что так оно будет только увеличиваться. А если мы ещё не достигли цели, функция пробует оба возможных пути, начинающихся с заданного числа. Она дважды вызывает себя, один раз с каждым из способов. Если первый вызов возвращает не null, он возвращается. В другом случае возвращается второй.

Чтобы лучше понять, как функция достигает нужного эффекта, давайте просмотрим её вызовы, которые происходят в поисках решения для числа 13.

```
find(1, "1")
  find(6, "(1 + 5)")
    find(11, "((1 + 5) + 5)")
      find(16, "(((1 + 5) + 5) + 5)")
        too big
      find(33, "(((1 + 5) + 5) * 3)")
        too big
    find(18, "((1 + 5) * 3)")
      too big
  find(3, "(1 * 3)")
    find(8, "((1 * 3) + 5)")
      find(13, "(((1 * 3) + 5) + 5)")
        found!
```

Отступ показывает глубину стека вызовов. В первый раз функция find вызывает сама себя дважды, чтобы проверить решения, начинающиеся с (1 + 5) и (1 * 3). Первый вызов ищет решение, начинающееся с (1 + 5), и при помощи рекурсии проверяет все решения, выдающие число, меньшее или равное требуемому. Не находит, и возвращает null. Тогда-то оператор || и переходит к вызову функции, который исследует вариант (1 * 3). Здесь нас ждёт удача, потому что в третьем рекурсивном вызове мы получаем 13. Этот вызов возвращает строку, и каждый из операторов || по пути передаёт эту строку выше, в результате возвращая решение.

##Выращиваем функции
Существует два более-менее естественных способа ввода функций в программу.

Первый – вы пишете схожий код несколько раз. Этого нужно избегать – больше кода означает больше места для ошибок и больше материала для чтения тех, кто пытается понять программу. Так что мы берём повторяющуюся функциональность, подбираем ей хорошее имя и помещаем её в функцию.

Второй способ – вы обнаруживаете потребность в некоей новой функциональности, которая достойна помещения в отдельную функцию. Вы начинаете с названия функции, и затем пишете её тело. Можно даже начинать с написания кода, использующего функцию, до того, как сама функция будет определена.

То, насколько сложно вам подобрать имя для функции, показывает, как хорошо вы представляете себе её функциональность. Возьмём пример. Нам нужно написать программу, выводящую два числа, количество коров и куриц на ферме, за которыми идут слова «коров» и «куриц». К числам нужно спереди добавить нули так, чтобы каждое занимало ровно три позиции.

```
007 Коров
011 Куриц
```

Очевидно, что нам понадобится функция с двумя аргументами. Начинаем кодить.
```
// вывестиИнвентаризациюФермы
function printFarmInventory(cows, chickens) {
  var cowString = String(cows);
  while (cowString.length < 3)
    cowString = "0" + cowString;
  console.log(cowString + " Коров");
  var chickenString = String(chickens);
  while (chickenString.length < 3)
    chickenString = "0" + chickenString;
  console.log(chickenString + " Куриц");
}
printFarmInventory(7, 11);
```

Если мы добавим к строке .length, мы получим её длину. Получается, что циклы while добавляют нули спереди к числам, пока не получат строчку в 3 символа.

Готово! Но только мы собрались отправить фермеру код (вместе с изрядным чеком, разумеется), он звонит и говорит нам, что у него в хозяйстве появились свиньи, и не могли бы мы добавить в программу вывод количества свиней?

Можно, конечно. Но когда мы начинаем копировать и вставлять код из этих четырёх строчек, мы понимаем, что надо остановиться и подумать. Должен быть способ лучше. Пытаемся улучшить программу:

```
// выводСДобавлениемНулейИМеткой
function printZeroPaddedWithLabel(number, label) {
  var numberString = String(number);
  while (numberString.length < 3)
    numberString = "0" + numberString;
  console.log(numberString + " " + label);
}

// вывестиИнвентаризациюФермы
function printFarmInventory(cows, chickens, pigs) {
  printZeroPaddedWithLabel(cows, "Коров");
  printZeroPaddedWithLabel(chickens, "Куриц");
  printZeroPaddedWithLabel(pigs, "Свиней");
}

printFarmInventory(7, 11, 3);
```

Работает! Но название printZeroPaddedWithLabel немного странное. Оно объединяет три вещи – вывод, добавление нулей и метку – в одну функцию. Вместо того, чтобы вставлять в функцию весь повторяющийся фрагмент, давайте выделим одну концепцию:

```
// добавитьНулей
function zeroPad(number, width) {
  var string = String(number);
  while (string.length < width)
    string = "0" + string;
  return string;
}

// вывестиИнвентаризациюФермы
function printFarmInventory(cows, chickens, pigs) {
  console.log(zeroPad(cows, 3) + " Коров");
  console.log(zeroPad(chickens, 3) + " Куриц");
  console.log(zeroPad(pigs, 3) + " Свиней");
}

printFarmInventory(7, 16, 3);
```

Функция с хорошим, понятным именем zeroPad облегчает понимание кода. И её можно использовать во многих ситуациях, не только в нашем случае. К примеру, для вывода отформатированных таблиц с числами.

Насколько умными и универсальными должны быть функции? Мы можем написать как простейшую функцию, которая дополняет число нулями до трёх позиций, так и навороченную функцию общего назначения для форматирования номеров, поддерживающую дроби, отрицательные числа, выравнивание по точкам, дополнение разными символами, и т.п.

Хорошее правило – добавляйте только ту функциональность, которая вам точно пригодится. Иногда появляется искушение создавать фреймворки общего назначения для каждой небольшой потребности. Сопротивляйтесь ему. Вы никогда не закончите работу, а просто напишете кучу кода, который никто не будет использовать.

##Функции и побочные эффекты
Функции можно грубо разделить на те, что вызываются из-за своих побочных эффектов, и те, что вызываются для получения некоторого значения. Конечно, возможно и объединение этих свойств в одной функции.

Первая вспомогательная функция в примере с фермой, printZeroPaddedWithLabel, вызывается из-за побочного эффекта: она выводит строку. Вторая, zeroPad, из-за возвращаемого значения. И это не совпадение, что вторая функция пригождается чаще первой. Функции, возвращающие значения, легче комбинировать друг с другом, чем функции, создающие побочные эффекты.

Чистая функция – особый вид функции, возвращающей значения, которая не только не имеет побочных эффектов, но и не зависит от побочных эффектов остального кода – к примеру, не работает с глобальными переменными, которые могут быть случайно изменены где-то ещё. Чистая функция, будучи вызванной с одними и теми же аргументами, возвращает один и тот же результат (и больше ничего не делает) – что довольно приятно. С ней просто работать. Вызов такой функции можно мысленно заменять результатом её работы, без изменения смысла кода. Когда вы хотите проверить такую функцию, вы можете просто вызвать её, и быть уверенным, что если она работает в данном контексте, она будет работать в любом. Не такие чистые функции могут возвращать разные результаты в зависимости от многих факторов, и иметь побочные эффекты, которые сложно проверять и учитывать.

Однако, не надо стесняться писать не совсем чистые функции, или начинать священную чистку кода от таких функций. Побочные эффекты часто полезны. Нет способа написать чистую версию функции console.log, и эта функция весьма полезна. Некоторые операции легче выразить, используя побочные эффекты.

##Итог
Эта глава показала вам, как писать собственные функции. Когда ключевое слово function используется в виде выражения, возвращает указатель на вызов функции. Когда оно используется как инструкция, вы можете объявлять переменную, назначая ей вызов функции.

```
// Создаём f со ссылкой на функцию
var f = function(a) {
  console.log(a + 2);
};

// Объявляем функцию g
function g(a, b) {
  return a * b * 3.5;
}
```

Ключевой момент в понимании функций – локальные области видимости. Параметры и переменные, объявленные внутри функции, локальны для неё, пересоздаются каждый раз при её вызове, и не видны снаружи. Функции, объявленные внутри другой функции, имеют доступ к её области видимости.

Очень полезно разделять разные задачи, выполняемые программой, на функции. Вам не придётся повторяться, функции делают код более читаемым, разделяя его на смысловые части, так же, как главы и секции книги помогают в организации обычного текста.

##Упражнения
###Минимум
В предыдущей главе была упомянута функция Math.min, возвращающая самый маленький из аргументов. Теперь мы можем написать такую функцию сами. Напишите функцию min, принимающую два аргумента, и возвращающую минимальный из них.

```
console.log(min(0, 10));
// → 0
console.log(min(0, -10));
// → -10
```

###Рекурсия
Мы видели, что оператор % (остаток от деления) может использоваться для определения того, чётное ли число ( % 2). А вот ещё один способ определения:

Ноль чётный.
Единица нечётная.
У любого числа N чётность такая же, как у N-2.

Напишите рекурсивную функцию isEven согласно этим правилам. Она должна принимать число и возвращать булевское значение.

Потестируйте её на 50 и 75. Попробуйте задать ей -1. Почему она ведёт себя таким образом? Можно ли её как-то исправить?

Test it on 50 and 75. See how it behaves on -1. Why? Can you think of a way to fix this?

```
console.log(isEven(50));
// → true
console.log(isEven(75));
// → false
console.log(isEven(-1));
// → ??
```

###Считаем бобы.

Символ номер N строки можно получить, добавив к ней .charAt(N) ( “строчка”.charAt(5) ) – схожим образом с получением длины строки при помощи .length. Возвращаемое значение будет строковым, состоящим из одного символа (к примеру, “к”). У первого символа строки позиция 0, что означает, что у последнего символа позиция будет string.length – 1. Другими словами, у строки из двух символов длина 2, а позиции её символов будут 0 и 1.

Напишите функцию countBs, которая принимает строку в качестве аргумента, и возвращает количество символов “B”, содержащихся в строке.

Затем напишите функцию countChar, которая работает примерно как countBs, только принимает второй параметр — символ, который мы будем искать в строке (вместо того, чтобы просто считать количество символов “B”). Для этого переделайте функцию countBs.