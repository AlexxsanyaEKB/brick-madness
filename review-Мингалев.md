# Code review:

## Что понравилось
В общем и целом игра, как порт тетриса, неплоха, отдельный лайк за загрузочный экран :thumbsup:. Блоки не сливаются друг с другом, нормально падают и поворачиваются (почти). Счётчик очков и отображение следующей фигуры приятно дополняют игровой процесс.

## Что не понравилось
Хотелось бы оптимизировать отрисовку, т.к. летящие фигуры "мерцают". Поворот длинной палки в уже установленные конструкции "сплавляет" её с конструкцией, желательно добавить проверку для поворота. День сурка надоедает спустя пару каток, хотелось бы некое подобие рандома.

## Что хотелось бы поправить
1. Сборка мусора: добавьте в классы dispose() методы, без них оперативная память не очщается, что может привести к переполнению стека спустя 2-3 итерации игры:
    Например для класса Point:

    method void dispose(){
        do Memory.deAlloc(this);
        return;
    }

    В классах, где используются переменные ссылочного типа нужно отдельно очищать каждую такую переменную своим dispose() внутри главного метода dispose().

2. Разделение: класс Game перегружен и плохо читается. Было бы здорово разбить его на вспомогательные, в соответствии с их областями ответственности, например, отрисовку меню вынести в Main, а для инициализации, отрисовки объектов и т.д. выделить отдельные классы.

3. В конструкторах классов логичнее сразу инициализировать все (по возможности) поля, например:

    constructor Game new() 
    {
        let screenHeight = 25;
        let screenWidth = 32;
        let wallsAndBricks = Array.new(screenWidth);
        //...
        return this;
    }

4. Лишний метод, к полям внутри класса можно напрямую обращаться.

     method boolean isOver()
    {  
        return isGameOver;
    }

5. Поправить названия переменных (will в начале bool не особо хорошо звучит), let pieceRow0 = Array.new(5) - не говорит о том, что это поворот фигуры.

6. Поправить названия некоторых методов, сложно определить что делает play() и в чём разница между drawBricks() и drawThisBrick(), не вникая контекст. readKey() вообще обновляет игровое состояние, хотя из названия полагается, что он будет только считывать нажатую клавишу.

7. Рекурсия в методе play() - не лучшая идея, учитывая наши технические ограничения в виде памяти (смотри п. 1). Лучше создать цикл в Main и вынести логику перезапуска по нажатию туда.

8. В инициализации фигур много дублирования кода. Большинство из них не больше 3 x 3, можно завести модульную генерацию, в котору передавать значения, где есть блок, а где нет, например для "квадрата с хвостиком":
    0 1 0
    1 1 0 
    1 1 0 
   Для 5 x 5 фигур, соответственно, отдельная генерация. 

9. Скорее косметическое улучшение, но открывающие фигурные скобки можно писать в строке объявления метода, это сократит инфраструктурный код.

10. Добавить README на github