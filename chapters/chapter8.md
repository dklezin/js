#Поиск и обработка ошибок

<i>Отладка изначально вдвое сложнее написания кода. Поэтому, если вы пишете код настолько заумный, насколько можете, то по определению вы не способны отлаживать его.
Брайан Керниган и П.Ж.Плауэр, «Основы программного стиля»</i>

<i>Юан-Ма написал небольшую программу, использующую много глобальных переменных и ужасных хаков. Ученик, читая программу, спросил его: «Вы предупреждали нас о подобных техниках, но при этом я нахожу их в вашей же программе. Как это возможно?» Мастер ответил: «Не нужно бежать за поливальным шлангом, если дом не горит».

Мастер Юан-Ма, «Книга программирования».</i>

Программа – это кристаллизованная мысль. Иногда мысли путаются. Иногда при превращении мыслей в программу в код вкрадываются ошибки. В обоих случаях получается повреждённая программа.

Недостатки в программах обычно называют ошибками. Это могут быть ошибки программиста или проблемы в системах, с которыми программа взаимодействует. Некоторые ошибки очевидны, другие – трудноуловимы, и могут скрываться в системах годами.

Часто проблема возникает в тех ситуациях, возникновение которых программист изначально не предвидел. Иногда этих ситуаций нельзя избежать. Когда пользователя просят ввести его возраст, а он вводит «апельсин», это ставит программу в непростую ситуацию. Эти ситуации необходимо предвидеть и как-то обрабатывать.

##Ошибки программистов
В случае ошибок программистов наша цель ясна. Нам надо найти их и исправить. Таковые ошибки варьируются от простых опечаток, на которые компьютер пожалуется сразу же, как только увидит программу, до скрытых ошибок в нашем понимании того, как программа работает, которые приводят к неправильным результатам в особых случаях. Ошибки последнего рода можно искать неделями.

Разные языки по-разному могут помогать вам в поиске ошибок. К сожалению, JavaScript находится на конце этой шкалы, обозначенном как «вообще почти не помогает». Некоторым языкам надо точно знать типы всех переменных и выражений ещё до запуска программы, и они сразу сообщат вам, если типы использованы некорректно. JavaScript рассматривает типы только во время исполнения программ, и даже тогда он разрешает делать не очень осмысленные вещи без всяких жалоб, например 

```
x = true * "обезьяна"
```

На некоторые вещи JavaScript всё-таки жалуется. Написание синтаксически неправильной программы сразу вызовет ошибку. Другие ошибки, например вызов чего-либо, не являющегося функцией, или обращение к свойству неопределённой переменной, возникнут при выполнении программы, когда она сталкивается с такой бессмысленной ситуацией.

Но часто ваши бессмысленные вычисления просто породят NaN (not a number) или undefined. Программа радостно продолжит, будучи уверенной в том, что она делает что-то осмысленное. Ошибка проявит себя позже, когда такое фиктивное значение уже пройдёт через несколько функций. Она может вообще не вызвать сообщение об ошибке, а просто привести к неправильному результату выполнения. Поиск источника таких проблем – сложная задача.

Процесс поиска ошибок (bugs) в программах называется отладкой (debugging).

##Строгий режим (strict mode)
JavaScript можно заставить быть построже, переведя его в строгий режим. Для этого наверху файла или тела функции пишется «use strict». Пример:

```
function canYouSpotTheProblem() {
  "use strict";
  for (counter = 0; counter < 10; counter++)
    console.log("Всё будет офигенно");
}

canYouSpotTheProblem();
// → ReferenceError: counter is not defined
```

Обычно, когда ты забываешь написать var перед переменной, как в примере перед counter, JavaScript по-тихому создаёт глобальную переменную и использует её. В строгом режиме выдаётся ошибка. Это очень удобно. Однако, ошибка не выдаётся, когда глобальная переменная уже существует – только тогда, когда присваивание создаёт новую переменную.

Ещё одно изменение – привязка this содержит undefined в тех функциях, которые вызывали не как методы. Когда мы вызываем функцию не в строгом режиме, this ссылается на объект глобальной области видимости. Поэтому если вы случайно неправильно вызовете метод в строгом режиме, JavaScript выдаст ошибку, если попытается прочесть что-то из this, а не будет радостно работать с глобальным объектом.

К примеру, рассмотрим код, вызывающий конструктор без ключевого слова new, в случае чего this не будет ссылаться на создаваемый объект.

```
function Person(name) { this.name = name; }
var ferdinand = Person("Евлампий"); // ой-вэй
console.log(name);
// → Евлампий
```

Некорректный вызов Person успешно происходит, но возвращается как undefined и создаёт глобальную переменную name. В строгом режиме всё по-другому:

```
"use strict";
function Person(name) { this.name = name; }
// Опаньки, мы ж забыли 'new'
var ferdinand = Person("Евлампий ");
// → TypeError: Cannot set property 'name' of undefined
```

Нам сразу сообщают об ошибке. Очень удобно.

Строгий режим умеет ещё кое-что. Он запрещает вызывать функцию с несколькими параметрами с одним и тем же именем, и удаляет некоторые потенциально проблемные свойства языка (например, инструкцию with, которая настолько ужасна, что даже не обсуждается в этой книге).

Короче говоря, надпись «use strict» перед текстом программы редко причиняет проблемы, зато помогает вам видеть их.

##Тестирование
Если язык не собирается помогать нам в поиске ошибок, приходится искать их сложным способом: запуская программу и наблюдая, делает ли она что-то так, как надо.

Делать это вручную, снова и снова – верный способ сойти с ума. К счастью, часто возможно написать другую программу, которая автоматизирует проверку вашей основной программы.

Для примера вновь обратимся к типу Vector.

```
function Vector(x, y) {
  this.x = x;
  this.y = y;
}
Vector.prototype.plus = function(other) {
  return new Vector(this.x + other.x, this.y + other.y);
};
```

Мы напишем программу, которая проверит, что наша реализация Vector работает, как нужно. Затем после каждого изменения реализации мы будем запускать проверочную программу, чтобы убедиться, что мы ничего не сломали. Когда мы добавим функциональности (к примеру, новый метод) к типу Vector, мы добавим проверок этой новой функциональности.

```
function testVector() {
  var p1 = new Vector(10, 20);
  var p2 = new Vector(-10, 5);
  var p3 = p1.plus(p2);

  if (p1.x !== 10) return "облом: x property";
  if (p1.y !== 20) return " облом: y property";
  if (p2.x !== -10) return " облом: negative x property";
  if (p3.x !== 0) return " облом: x from plus";
  if (p3.y !== 25) return " облом: y from plus";
  return "всё пучком";
}
console.log(testVector());
// → всё пучком
```

Написание таких проверок приводит к появлению повторяющегося кода. К счастью, есть программные продукты, помогающие писать наборы проверок при помощи специального языка, приспособленного именно для написания проверок. Их называют testing frameworks.

##Отладка (debugging)
Когда вы заметили проблему в программе,– она ведёт себя неправильно и выдаёт ошибки,- самое время выяснить, в чём проблема.

Иногда это очевидно. Сообщение об ошибке наводит вас на конкретную строку программы, и если вы прочтёте описание ошибки и эту строку, вы часто сможете найти проблему.

Но не всегда. Иногда строчка, приводящая к ошибке, просто оказывается первым местом, где некорректное значение, полученное где-то ещё, используется неправильно. Иногда вообще нет сообщения об ошибке – есть просто неверный результат. Если вы делали упражнения из предыдущих глав, вы наверняка попадали в такие ситуации.

Следующий пример пробует преобразовать число заданной системы счисления в строку, отнимая последнюю цифру и совершая деление, чтобы избавиться от этой цифры. Но дикий результат, выдаваемый программой, как бы намекает на присутствие в ней ошибки.

```
function numberToString(n, base) {
  var result = "", sign = "";
  if (n < 0) {
    sign = "-";
    n = -n;
  }
  do {
    result = String(n % base) + result;
    n /= base;
  } while (n > 0);
  return sign + result;
}
console.log(numberToString(13, 10));
// → 1.5e-3231.3e-3221.3e-3211.3e-3201.3e-3191.3e-3181.3…
```

Даже если вы нашли проблему – притворитесь, что ещё не нашли. Мы знаем, что программа сбоит, и нам нужно узнать, почему.

Здесь вам надо преодолеть желание начать вносить случайные изменения в код. Вместо этого подумайте. Проанализируйте результат и придумайте теорию, по которой это происходит. Проведите дополнительные наблюдения для проверки теории – а если теории нет, проведите наблюдения, которые бы помогли вам изобрести её.

Размещение нескольких вызовов console.log в стратегических местах – хороший способ получить дополнительную информацию о том, что программа делает. В нашем случае нам нужно, чтобы n принимала значения 13, 1, затем 0. Давайте выведем значения в начале цикла:

```
13
1.3
0.13
0.013
…
1.5e-323
```

Н-да. Деление 13 на 10 выдаёт не целое число. Вместо n /= base нам нужно n = Math.floor(n / base), тогда число будет корректно «сдвинуто» вправо.

Кроме console.log можно воспользоваться отладчиком в браузере. Современные браузеры умеют ставить точку остановки на выбранной строчке кода. Это приведёт к приостановке выполнения программы каждый раз, когда будет достигнута выбранная строчка, и тогда вы сможете просмотреть содержимое переменных. Не буду подробно расписывать процесс, поскольку у разных браузеров он организован по-разному – поищите в вашем браузере “developer tools”, инструменты разработчика. Ещё один способ установить точку остановки – включить в код инструкцию для отладчика, состоящую из ключевого слова debugger. Если инструменты разработчика активны, исполнение программы будет приостановлено на этой инструкции, и вы сможете изучить состояние программы.

##Распространение ошибок
К сожалению, программист может предотвратить появление не всех проблем. Если ваша программа общается с внешним миром, она может получить неправильные входные данные, или же системы, с которыми она пытается взаимодействовать, окажутся сломанными или недоступными.

Простые программы, или программы, работающие под вашим надзором, могут просто «сдаваться» в такой момент. Вы можете изучить проблему и попробовать снова. «Настоящие» приложения не должны просто «падать». Иногда приходится принимать неправильные входные данные и как-то с ними работать. В других случаях, нужно сообщить пользователю, что что-то пошло не так – и потом уже сдаваться. В любом случае программа должна что-то сделать в ответ на возникновение проблемы.

Допустим, у вас есть функция promptInteger, которая запрашивает целое число и возвращает его. Что она должна сделать, если пользователь введёт «апельсин»?

Один из вариантов – вернуть особое значение. Обычно для этих целей используют null и undefined.

```
function promptNumber(question) {
  var result = Number(prompt(question, ""));
  if (isNaN(result)) return null;
  else return result;
}

console.log(promptNumber("Сколько пальцев видите?"));
```

Это надёжная стратегия. Теперь любой код, вызывающий promptNumber, должен проверять, было ли возвращено число, и если нет, как-то выйти из ситуации – спросить снова, или задать значение по-умолчанию. Или вернуть специальное значение уже тому, кто его вызвал, сообщая о неудаче.

Во многих таких случаях, когда ошибки возникают часто и вызывающий функцию код должен принимать их во внимание, совершенно допустимо возвращать специальное значение как индикатор ошибки. Но есть и минусы. Во-первых, что, если функция и так может вернуть любой тип значения? Для неё сложно найти специальное значение, которое будет отличаться от допустимого результата.

Вторая проблема – работа со специальными значениями может замусорить код. Если функция promptNumber вызывается 10 раз, то надо 10 раз проверить, не вернула ли она null. Если реакция на null заключается в возврате null на уровень выше, тогда там, где вызывался этот код, тоже нужно встраивать проверку на null, и так далее.

##Исключения
Когда функция не может работать нормально, мы бы хотели остановить работу и перепрыгнуть туда, где такая ошибка может быть обработана. Этим занимается обработка исключений.

Код, встретивший проблему в момент выполнения, может поднять (или выкинуть) исключение (raise exception, throw exception), которое представляет из себя некое значение. Возврат исключения напоминает некий «прокачанный» возврат из функции – он выпрыгивает не только из самой функции, но и из всех вызывавших её функций, до того места, с которого началось выполнение. Это называется развёртыванием стека (unwinding the stack). Может быть, вы помните стек функций из главы 3… Исключение быстро проматывает стек вниз, выкидывая все контексты вызовов, которые встречает.

Если бы исключения сразу доходили до самого низа стека, пользы от них было бы немного. Они бы просто предоставляли интересный способ взорвать программу. Их сила в том, что на их пути в стеке можно поставить «препятствия», которые будут ловить исключения, мчащиеся по стеку. И тогда с этим можно сделать что-то полезное, после чего программа продолжает выполняться с той точки, где было поймано исключение.

Пример:

```
function promptDirection(question) {
  var result = prompt(question, "");
  if (result.toLowerCase() == "left") return "L";
  if (result.toLowerCase() == "right") return "R";
  throw new Error("Недопустимое направление: " + result);
}

function look() {
  if (promptDirection("Куда?") == "L")
    return "дом";
  else
    return "двоих разъярённых медведей";
}

try {
  console.log("Вы видите ", look());
} catch (error) {
  console.log("Что-то не так: " + error);
}
```

Ключевое слово throw используется для выбрасывания исключения. Ловлей занимается кусок кода, обёрнутый в блок try, за которым следует catch. Когда код в блоке try выкидывает исключение, выполняется блок catch. Переменная, указанная в скобках, будет привязана к значению исключения. После завершения выполнения блока catch, или же если блок try выполняется без проблем, выполнение переходит к коду, лежащему после инструкции try/catch.

В данном случае для создания исключения мы использовали конструктор Error. Это стандартный конструктор, создающий объект со свойством message. В современных окружениях JavaScript экземпляры этого конструктора также собирают информацию о стеке вызовов, который был накоплен в момент выкидывания исключения – так называемое отслеживание стека (stack trace). Эта информация сохраняется в свойстве stack, и может помочь при разборе проблемы – она сообщает, в какой функции случилась проблема и какие другие функции привели к данному вызову.

Обратите внимание, что функция look полностью игнорирует возможность возникновения проблем в promptDirection. Это преимущество исключений – код, обрабатывающий ошибки, нужен только в том месте, где происходит ошибка, и там, где она обрабатывается. Промежуточные функции просто не обращают на это внимания.

Ну, почти.

##Подчищаем за исключениями
Представьте следующую ситуацию: функция withContext желает удостовериться, что во время её выполнения переменная верхнего уровня context получит специальное значение контекста. После завершения выполнения, она восстанавливает её старое значение.

var context = null;

```
function withContext(newContext, body) {
  var oldContext = context;
  context = newContext;
  var result = body();
  context = oldContext;
  return result;
}
```

Что, если функция body выбросит исключение? В таком случае вызов withContext будет выброшен исключением из стека, и переменной context никогда не будет возвращено первоначальное значение.

Но у инструкции try есть ещё одна особенность. За ней может следовать блок finally, либо вместо catch, либо вместе с catch. Блок finally означает «выполнить код в любом случае после выполнения блока try”. Если функции надо что-то подчистить, то подчищающий код нужно включать в блок finally.

```
function withContext(newContext, body) {
  var oldContext = context;
  context = newContext;
  try {
    return body();
  } finally {
    context = oldContext;
  }
}
```

Заметьте, что нам больше не нужно сохранять результат вызова body в отдельной переменной, чтобы вернуть его. Даже если мы возвращаемся из блока try, блок finally всё равно будет выполнен. Теперь мы можем безопасно сделать так:

```
try {
  withContext(5, function() {
    if (context < 10)
      throw new Error("Контекст слишком мал!");
  });
} catch (e) {
  console.log("Игнорируем: " + e);
}
// → Игнорируем: Error: Контекст слишком мал!

console.log(context);
// → null
```

Несмотря на то, что вызываемая из withContext функция «сломалась», сам по себе withContext по-прежнему подчищает значение переменной context.

##Выборочный отлов исключений
Когда исключение доходит до низа стека и его никто не поймал — его обрабатывает окружение. Как именно – зависит от конкретного окружения. В браузерах описание ошибки выдаётся в консоль (она обычно доступна в меню «Инструменты» или «Разработка»).

Если речь идёт об ошибках или проблемах, которые программа не может обработать в принципе, допустимо просто пропустить такую ошибку. Необработанное исключение – разумный способ сообщить о проблеме в программе, и консоль в современных браузерах выдаст вам необходимую информацию о том, какие вызовы функций были в стеке в момент возникновения проблемы.

Если возникновение проблемы предсказуемо, программа не должна падать с необработанным исключением — это не очень дружественно по отношению к пользователю.

Недопустимое использование языка – ссылки на несуществующую переменную, запрос свойств у переменной, равной null, или вызов чего-то, что не является функцией, тоже приводит к выбрасыванию исключений. Такие исключения можно отлавливать точно так же, как свои собственные.

При входе в блок catch мы знаем только, что что-то внутри блока try привело к исключению. Мы не знаем, что именно, и какое исключение произошло.

JavaScript (что является вопиющим упущением) не предоставляет непосредственной поддержки выборочного отлова исключений: либо ловим все, либо никакие. Из-за этого люди часто предполагают, что случившееся исключение – именно то, ради которого и писался блок catch.

Но может быть и по-другому. Нарушение произошло где-то ещё, или в программу вкралась ошибка. Вот пример, где мы пробуем вызывать promptDirection до тех пор, пока не получим допустимый ответ:

```
for (;;) {
  try {
    var dir = promtDirection("Куда?"); // ← опечатка!
    console.log("Ваш выбор ", dir);
    break;
  } catch (e) {
    console.log("Недопустимое направление. Попробуйте ещё раз.");
  }
}
```

Конструкция for (;;) – способ устроить бесконечный цикл. Мы вываливаемся из него, только когда получаем допустимое направление. Но мы неправильно написали название promptDirection, что приводит к ошибке “undefined variable”. А так как блок catch игнорирует значение исключения e, предполагая, что он разбирается с другой проблемой, он считает, что выброшенное исключение является результатом неправильных входных данных. Это приводит к бесконечному циклу и скрывает полезное сообщение об ошибке насчёт неправильного имени переменной.

Как правило, не стоит так ловить исключения, если только у вас нет цели перенаправить их куда-либо – к примеру, по сети, чтобы сообщить другой системе о падении нашей программы. И даже тогда внимательно смотрите, не будет ли скрыта важная информация.

Значит, нам надо поймать определённое исключение. Мы можем в блоке catch проверять, является ли случившееся исключение интересующим нас исключением, а в противном случае заново выбрасывать его. Но как нам распознать исключение?

Конечно, мы могли бы сравнить свойство message с сообщением об ошибке, которую мы ждём. Но это ненадёжный способ писать код – использовать информацию, предназначающуюся для человека (сообщение), чтобы принять программное решение. Как только кто-нибудь поменяет или переведёт это сообщение, код перестанет работать.

Давайте лучше определим новый тип ошибки и используем instanceof для его распознавания.

```
function InputError(message) {
  this.message = message;
  this.stack = (new Error()).stack;
}
InputError.prototype = Object.create(Error.prototype);
InputError.prototype.name = "InputError";
```

Прототип наследуется от Error.prototype, поэтому instanceof Error тоже будет выполняться для объектов типа InputError. И ему назначено свойство name, как и другим стандартным типам ошибок (Error, SyntaxError, ReferenceError, и т.п.)

Присвоение свойству stack пытается передать этому объекту отслеживание стека, на тех платформах, которые это поддерживают, путём создания объекта Error и использования его стека.

Теперь promptDirection может сотворить такую ошибку.

```
function promptDirection(question) {
  var result = prompt(question, "");
  if (result.toLowerCase() == "left") return "L";
  if (result.toLowerCase() == "right") return "R";
  throw new InputError("Invalid direction: " + result);
}

А в цикле её будет ловить сподручнее.

for (;;) {
  try {
    var dir = promptDirection("Куда?");
    console.log("Ваш выбор", dir);
    break;
  } catch (e) {
    if (e instanceof InputError)
      console.log("Недопустимое направление. Попробуйте ещё раз.");
    else
      throw e;
  }
}
```

Код отлавливает только экземпляры InputError и пропускает другие исключения. Если вы снова сделаете такую же опечатку, будет корректно выведено сообщение о неопределённой переменной.

##Утверждения (Assertions)
Утверждения – инструмент для простой проверки ошибок. Рассмотрим вспомогательную функцию assert:

```
function AssertionFailed(message) {
  this.message = message;
}
AssertionFailed.prototype = Object.create(Error.prototype);

function assert(test, message) {
  if (!test)
    throw new AssertionFailed(message);
}

function lastElement(array) {
  assert(array.length > 0, "пустой массив в lastElement");
  return array[array.length - 1];
}
```

Это – компактный способ ужесточения требований к значениям, который выбрасывает исключение в случае, когда заданное условие не выполняется. К примеру, функция lastElement, добывающая последний элемент массива, вернула бы undefined для пустых массивов, если б мы не использовали assertion. Извлечение последнего элемента пустого массива не имеет смысла, и это явно было бы ошибкой программиста.

Утверждения – способ убедиться в том, что ошибки провоцируют прерывание программы в том месте, где они совершены, а не просто выдают странные величины, которые передаются по системе и вызывают проблемы в каких-то других, не связанных с этим, местах.

##Итог
Ошибки и недопустимые входные данные случаются в жизни. Ошибки в программах надо искать и исправлять. Их легче найти, используя автоматические системы проверок и добавляя утверждения в ваши программы.

Проблемы, вызванные чем-то, что неподвластно вашей программе, нужно обрабатывать достойно. Иногда, когда проблему можно решить локально, допустимо возвращать специальные значения для отслеживания таких случаев. В других случаях предпочтительно использовать исключения.

Выброс исключения приводит к разматыванию стека до тех пор, пока не будет встречен блок try/catch или пока мы не дойдём до дна стека. Значение исключения будет передано в блок catch, который сможет удостовериться в том, что это исключение действительно то, которое он ждёт, и обработать его. Для работы с непредсказуемыми событиями в потоке программы можно использовать блоки finally, чтобы определённые части кода были выполнены в любом случае.

##Упражнения
###Повтор
Допустим, у вас есть функция primitiveMultiply, которая в 50% случаев перемножает 2 числа, а в остальных случаях выбрасывает исключение типа MultiplicatorUnitFailure. Напишите функцию, обёртывающую эту, и просто вызывающую её до тех пор, пока не будет получен успешный результат.

Убедитесь, что вы обрабатываете только нужные вам исключения.

```
function MultiplicatorUnitFailure() {}

function primitiveMultiply(a, b) {
  if (Math.random() < 0.5)
    return a * b;
  else
    throw new MultiplicatorUnitFailure();
}

function reliableMultiply(a, b) {
  // Ваш код
}

console.log(reliableMultiply(8, 8));
// → 64
```

###Запертая коробка
Рассмотрим такой, достаточно надуманный, объект:

```
var box = {
  locked: true,
  unlock: function() { this.locked = false; },
  lock: function() { this.locked = true;  },
  _content: [],
  get content() {
    if (this.locked) throw new Error("Заперто!");
    return this._content;
  }
};
```

Это коробочка с замком. Внутри лежит массив, но до него можно добраться только, когда коробка не заперта. Напрямую обращаться к свойству _content нельзя.

Напишите функцию withBoxUnlocked, принимающую в качестве аргумента функцию, которая отпирает коробку, выполняет функцию, и затем обязательно запирает коробку снова перед выходом – неважно, выполнилась ли переданная функция правильно, или она выбросила исключение.

```
function withBoxUnlocked(body) {
  // Ваш код
}

withBoxUnlocked(function() {
  box.content.push("золотишко");
});

try {
  withBoxUnlocked(function() {
    throw new Error("Пираты на горизонте! Отмена!");
  });
} catch (e) {
  console.log("Произошла ошибка:", e);
}
console.log(box.locked);
// → true
```

В качестве призовой игры убедитесь, что при вызове withBoxUnlocked, когда коробка не заперта, коробка остаётся незапертой.