# Задачи по Fortran

## Задача #1

#### _Задание:_
Дана квадратная матрица. Проверить положительна ли сумма под главной диагональю. Если да, то найти над главной диагональю максимальный четный элемент.

---
#### _Логика:_
Пройтись под дигональю для поиска суммы и над диагональю для поиска миксимального четного. Рассмотрим матрицу:
| 1  | 2  | 3  | 4  |
|----|----|----|----|
| 5  | 6  | 7  | 8  |
| 9  | 10 | 11 | 12 |
| 13 | 14 | 15 | 16 |

Главная диагональ проходит из левого верхнего угла в правый нижний. Индексы по строкам и столбцам у этих элементом совпадают.
Индексы главной диагонали:
|  i  |  1  |  2  |  3  |  4  |
|-----|-----|-----|-----|-----|
|**j**|**1**|**2**|**3**|**4**|

Если **j > i**, то элемент находится выше главной диагонали.
Если **j < i**, то элемент находится ниже главной диагонали.

Для прохода по всем элементам выше главной диагонали необходимо использовать вложенный цикл **for**.\
Для **i** необходимо пройтись от **1** до **n - 1**, где n - размер матрицы. Последнюю строку можно не брать, справа от диагонали тут ничего нет.
Для **j** необходимо пройтись от **i + 1** до **n**, где n - размер матрицы.

Проход по всем элементам выше диагонали выглядит следующим образом:
```fortran
do i = 1, n - 1
  do j = i + 1, n
    ! Обработка элемента matrix(i, j)
  end do
end do
```

Для прохода по всем элементам ниже главной диагонали необходимо использовать вложенный цикл **for**.\
Для **i** необходимо пройтись от **2** до **n**, где n - количество строк. Первую строку можно не брать, слева от диагонали тут ничего нет.
Для **j** необходимо пройтись от **1** до **i - 1**, ибо элемент с индексом **i** - это элемент диагонали.

Проход по всем элементам ниже диагонали выглядит следующим образом:
```fortran
do i = 2, n
  do j = 1, i - 1
    ! Обработка элемента matrix(i, j)
  end do
end do
```

С индексами разобрались. Рассмотрим алгоритм поиска суммы. Необходимо просто пройтись под диагональю матрицы вложенным циклом и прибавлять каждый элемент матрицы к сумме.
Это выглядит следующим образом:
```fortran
integer :: s = 0
do i = 2, n
  do j = 1, i - 1
    s = s + matrix(i, j)
  end do
end do
```

Для поиска максимального четного элемента надо сделать похожие действия. Пройтись по матрице, сравнивать каждый элемент с текущим максимальным и проверять его на четность. *Изначально максимальное значение должно быть очень маленьким. Настолько маленького элемента не должно быть в матрице.*
```fortran
integer :: maxvalue = -100000
do i = 1, n - 1
  do j = i + 1, n
    if (matrix(i, j) > maxvalue .and. mod(matrix(i, j), 2) == 0) then
      maxvalue = matrix(i, j)
    end if
  end do
end do
```

---
#### _Решение:_
```fortran
program heh
    integer, parameter :: nmax = 100 ! Константа для обозначения максимального размера матрицы
    integer :: infile = 1, outfile = 2 ! Входной и выходной файлы
    integer n ! Размер матрицы
    integer A(nmax, nmax) ! Матрица
    integer s, maxvalue ! Сумма и максимальное значение
    
    open(infile, file = 'in.txt') ! Открываем входной файл
    open(outfile, file = 'out.txt') ! Открываем выходной файл
    
    call inMatrix(infile, A, n, n) ! Вызываем подпрограмму для ввода данных из файла в матрицу. Принимает файл, матрицу, размеры
    
    call getSum(A, n, n, s) ! Вызываем подпрограмму для поиска суммы. Принимает матрицу, размеры, сумму
    if (s > 0) then ! Сравниваем сумму с нулем
        call getMax(A, n, n, maxvalue) ! Вызываем подпрограмму для поиска максимального
        if (maxvalue /= -10000) then ! Сравниваем его с установленным
            write(outfile, *) maxvalue ! Записать в файл максимальное значение
        else
            write(outfile, *) "Такого элемента в матрице нет"
        end if
    else
        write(outfile, *) "Сумма элементов под диагональю отрицательная" 
    end if
    
    close(infile) ! Закрываем входной файл
    close(outfile) ! Закрываем выходной файл
    contains
        subroutine inMatrix(file, matrix, row, col) ! Подпрограмма для ввода матрицы (файл, матрица, размеры)
            integer file, row, col, i, j ! Объявление переменных
            integer matrix(nmax, nmax)
            read(file, *) row ! Чтение первого числа из файла
            read(file, *) col ! Чтение второгой числа из файла
            read(file, *) ((matrix(i,j), j = 1, col), i = 1, row) ! Чтение всех остальних чисел и запись их в матрицу
        end subroutine inMatrix

        subroutine getSum(matrix, row, col, s) ! Подпрограмма для поиска суммы (матрица, размеры, сумма)
            integer row, col, i, j ! Объявление переменных
            integer matrix(nmax, nmax), s
            s = 0 ! Изначально сумма равна нулю
            do i = 2, row ! Цикл For
                do j = 1, i - 1 ! Цикл For
                    s = s + matrix(i, j) ! Прибавляем к сумме
                end do
            end do
        end subroutine getSum
        
        subroutine getMax(matrix, row, col, m) ! Подпрограмма для поиска максимума (матрица, размеры, максимум)
            integer row, col, i, j ! Объявление переменных
            integer matrix(nmax,nmax), m
            m = -10000 ! Изначально число должно быть очень маленьким
            do i = 1, row - 1 ! Цикл For
                do j = i + 1, col ! Цикл For
                    if (matrix(i, j) > m .and. mod(matrix(i, j), 2) == 0) then ! Сравнение с нулем и четностью. mod принимает число для сравнения и на что делить.
                        m = matrix(i, j)
                    end if
                end do
            end do
        end subroutine getMax
end program heh
```
---

## Задача #2

#### _Задание:_
Дана квадратная матрица. Если максимальный отрицательный элемент находится ниже главной диагонали, то составить массив из минимальных элементов строк. 

---
#### _Логика:_
Пройтись под дигональю для поиска минимального элемента. После этого пройтись над диагональю для поиска более меньшего. Если он не найден, то составить массив из минимальных элеметов строк. Для этого просто пройтись по строкам в поисках минимума. Информацию о том, находится ли миксимальный отрицательный элемент ниже диагонали, будем хранить в логической переменной.

**Теория по диагоналям и прохождениям представлена в Задаче #1.**

Изначально минимальным элементов в строке будет первый. Для поиска минимума в строке будем использовать следующий алгоритм:
```fortran
do i = 1, n
  m = matrix(i, j)
  do j = 1, n
    if (matrix(i, j) < m) then
      m = matrix(i, j)
    end if
  end do
  ! Обработка нашего минимума
end do

```


---
#### _Решение:_
```fortran
program hehe
    integer, parameter :: nmax = 100
    integer :: infile = 1, outfile = 2
    integer row, col, i
    real A(nmax, nmax), array(nmax)
    logical :: isDown = .true. ! Тут будем хранить находится ли нужное число ниже диагонали

    open(infile, file = 'in.txt')
    open(outfile, file = 'out.txt')
    
    call inMatrix(infile, A, row, col)
    
    call getPositionMax(A, row, col, isDown)
    if (isDown) then
        call createArrayMin(A, row, col, array) ! Если ниже, то создаем массив и выводим его в файл
        call outArray(outfile, array, row)
    end if
    
    close(infile)
    close(outfile)
    
    contains
        subroutine inMatrix(file, matrix, row, col)
            integer file
            integer row, col
            real matrix(nmax,nmax)
            integer i, j
            read(file, *) row
            read(file, *) col
            read(file, *) ((matrix(i,j), j = 1, col), i = 1, row)
        end subroutine inMatrix

        subroutine outArray(file, array, col) ! Подпрограмма для вывода массива в файл
            integer file, col
            real array(nmax)
            integer i, j
            character(20) col_str
            write(col_str, *) col
            write(file, '(' // col_str // '(1X,F6.2))') (array(j), j = 1, col)
        end subroutine outArray
        
        subroutine createArrayMin(matrix, row, col, array)
            integer row, col, i, j
            real matrix(nmax,nmax), array(nmax)
            real minvalue
            
            do i = 1, row
                minvalue = matrix(i, 1)
                do j = 2, col
                    if (matrix(i, j) < minvalue) then
                        minvalue = matrix(i, j)
                    end if
                end do
                array(i) = minvalue
            end do
                    
        end subroutine createArrayMin
        
        subroutine getPositionMax(matrix, row, col, isDown)
            integer row, col, i, j
            real matrix(nmax,nmax)
            logical isDown
            real maxvalue
            maxvalue = -10000000
            
            do i = 2, row
                do j = 1, i - 1
                    if (matrix(i, j) < 0 .and. matrix(i, j) > maxvalue) then
                        maxvalue = matrix(i, j)
                    end if
                end do
            end do
            
            do i = 1, row - 1
                do j = i + 1, col
                    if (matrix(i, j) < 0 .and. matrix(i, j) > maxvalue) then
                        isDown = .false.
                    end if
                end do
            end do
        end subroutine getPositionMax
end program hehe
```
---

## Задача #3

#### _Задание:_
Дана матрица А, если среднее арифметическое чётных элементов больше нуля, то сформировать одномерный массив С из элементов А, которые больше этого среднего арифметического.

---
#### _Логика:_
Пройтись по всей матрице для подсчета суммы. Поделить сумму на количество элементо в матрице. Если оно больше нуля, то пройтись по матрице еще раз и сравнить каждый элемент с полученным числом. Создать и вывести новый массив.

Алгоритм для поиска среднего арифметического четных элементов:
```fortran
integer :: c = 0
do i = 1, row
  do j = 1, col
    if (mod(matrix(i, j), 2) == 0) then
      s = s + matrix(i, j)
      c = c + 1
    end if
  end do
end do
r = s / c
```


---
#### _Решение:_
```fortran
program hehe
    integer, parameter :: nmax = 100
    integer :: infile = 1, outfile = 2
    integer row, col, A(nmax, nmax), array(nmax * nmax)
    real r ! Среднее арифметические
    integer :: n = 1 ! Количество элементов в новом массиве

    open(infile, file = 'inA.txt')
    open(outfile, file = 'out.txt')
    
    call inMatrix(infile, A, row, col)
    
    call getArithmeticMean(A, row, col, r)
    if (r > 0) then
        call createArray(A, row, col, array, r, n)
        if (n == 1) then
            write(outfile, *) "Таких чисел нет"
        else 
            call outArray(outfile, array, n - 1)
        end if
    else
        write(outfile, *) "Среднее арифметическое четных чисел меньше нуля"
    end if
    
    close(infile)
    close(outfile)
    
    contains
        subroutine inMatrix(file, matrix, row, col)
            integer file, row, col, i, j
            integer matrix(nmax,nmax)
            read(file, *) row
            read(file, *) col
            read(file, *) ((matrix(i,j), j = 1, col), i = 1, row)
        end subroutine inMatrix

        subroutine outArray(file, array, col)
            integer file, col, i, j
            integer array(nmax)
            character(20) col_str
            write(col_str, *) col
            write(file, *) (array(j), j = 1, col)
        end subroutine outArray
        
        subroutine createArray(matrix, row, col, array, value, n)
            integer row, col, i, j, n
            real value
            integer matrix(nmax, nmax), array(nmax * nmax)
            
            do i = 1, row
                do j = 1, col
                    if (matrix(i, j) > value) then
                        array(n) = matrix(i, j)
                        n = n + 1
                    end if
                end do
            end do
                    
        end subroutine createArray
        
        subroutine getArithmeticMean(matrix, row, col, r)
            integer row, col, i, j
            real r
            integer matrix(nmax, nmax)
            real :: s = 0 ! Сумма элементов
            integer :: c = 0 ! Количество элементов
            
            do i = 1, row
                do j = 1, col
                    if (mod(matrix(i, j), 2) == 0) then
                        s = s + matrix(i, j)
                        c = c + 1
                    end if
                end do
            end do
            r = s / c
        end subroutine getArithmeticMean
end program hehe
```
---

## Задача #4

#### _Задание:_
Дана двумерная матрица. Найти первый столбец, в котором есть хотя бы один чётный элемент, и найти сумму столбцов после этого столбца.

---
#### _Логика:_
Пройтись по всей матрице для поиска четного. Как только нашли, запомнить номер столбцы. Пройтись уже от следующего столбца для когда для поиска суммы. Вывести ее

Алгоритм для поиска столбца с четным элементов будет следующим:
```fortran
integer :: pos = 0
do j = 1, col
    do i = 1, row
        if (mod(matrix(i, j), 2) == 0) then
            pos = j
            exit ! С помощью этого можно прервать цикл и выйти из него
        end if
    end do
    if (pos /= 0) then
        exit
    end if
end do
```
---
#### _Решение:_
```fortran
program hehe
    integer, parameter :: nmax = 100
    integer :: infile = 1, outfile = 2
    integer row, col, A(nmax, nmax)
    integer :: s = 0, pos = 0

    open(infile, file = 'inA.txt')
    open(outfile, file = 'out.txt')
    
    call inMatrix(infile, A, row, col)
    
    call getColumn(A, row, col, pos)
    if (pos > 0 .and. pos < col) then
        call getSum(A, row, col, pos + 1, s)
        write(outfile, *) s
    else
        write(outfile, *) "Такого столбца не существует"
    end if
    
    close(infile)
    close(outfile)
    
    contains
        subroutine inMatrix(file, matrix, row, col)
            integer file, row, col, i, j
            integer matrix(nmax,nmax)
            read(file, *) row
            read(file, *) col
            read(file, *) ((matrix(i,j), j = 1, col), i = 1, row)
        end subroutine inMatrix

        subroutine outArray(file, array, col)
            integer file, col, i, j
            integer array(nmax)
            character(20) col_str
            write(col_str, *) col
            write(file, *) (array(j), j = 1, col)
        end subroutine outArray
        
        subroutine getSum(matrix, row, col, pos, s)
            integer row, col, i, j, pos, s
            integer matrix(nmax, nmax)
            
            do i = 1, row
                do j = pos, col
                    s = s + matrix(i, j)
                end do
            end do
                    
        end subroutine getSum
        
        subroutine getColumn(matrix, row, col, pos)
            integer row, col, i, j, pos
            integer matrix(nmax, nmax)
            
            do j = 1, col
                do i = 1, row
                    if (mod(matrix(i, j), 2) == 0) then
                        pos = j
                        exit
                    end if
                end do
                if (pos /= 0) then
                    exit
                end if
            end do
        end subroutine getColumn
end program hehe
```
---

## Задача #5

#### _Задание:_
Если в матрице А отрицательных элементов больше, чем положительных, то в массив В вывести максимумы каждой строки матрицы А.

---
#### _Логика:_
Пройтись по всей матрице для подсчета количества отрицательных элементов. Если их больше, то пройтись по строкам и поискать там максимумы.

Алгоритм для поиска количества отрицательных элементов:
```fortran
do j = 1, col
    do i = 1, row
        if (matrix(i, j) < 0) then
            c = c + 1
        end if
    end do
end do
```
---
#### _Решение:_
```fortran
program hehe
    integer, parameter :: nmax = 100
    integer :: infile = 1, outfile = 2
    integer row, col, A(nmax, nmax), B(nmax)
    integer :: n = 0

    open(infile, file = 'inA.txt')
    open(outfile, file = 'out.txt')
    
    call inMatrix(infile, A, row, col)
    
    call getNegativeCount(A, row, col, n)
    if (n > (row * col - n)) then
        call createArray(A, row, col, B)
        call outArray(outfile, B, row)
    else
        write(outfile, *) "Отрицательных элементов матрицы меньше"
    end if
    
    close(infile)
    close(outfile)
    
    contains
        subroutine inMatrix(file, matrix, row, col)
            integer file, row, col, i, j
            integer matrix(nmax,nmax)
            read(file, *) row
            read(file, *) col
            read(file, *) ((matrix(i,j), j = 1, col), i = 1, row)
        end subroutine inMatrix

        subroutine outArray(file, array, col)
            integer file, col, i, j
            integer array(nmax)
            character(20) col_str
            write(col_str, *) col
            write(file, *) (array(j), j = 1, col)
        end subroutine outArray
        
        subroutine createArray(matrix, row, col, array)
            integer row, col, i, j, m
            integer matrix(nmax, nmax), array(nmax)
            
            do i = 1, row
                m = matrix(i, j)
                do j = 1, col
                    if (matrix(i, j) > m) then
                        m = matrix(i, j)
                    end if
                end do
                array(i) = m
            end do
                    
        end subroutine createArray
        
        subroutine getNegativeCount(matrix, row, col, n)
            integer row, col, i, j, n
            integer matrix(nmax, nmax)
            
            do j = 1, col
                do i = 1, row
                    if (matrix(i, j) < 0) then
                        n = n + 1
                    end if
                end do
            end do
        end subroutine getNegativeCount
end program hehe
```

## Задача #6

#### _Задание:_
Даны два одномерных массива X и Y. Сформировать из них массив Z с элементами, которые содержатся в X и не содержатся в Y, и которые есть в Y, но нет в X. Найти минимальный положительный элемент в массиве Z.

---
#### _Логика:_
Пройтись сразу по двум массивам 

Алгоритм для поиска количества отрицательных элементов:
```fortran
do j = 1, col
    do i = 1, row
        if (matrix(i, j) < 0) then
            c = c + 1
        end if
    end do
end do
```
---
#### _Решение:_
```fortran
program hehe
    integer, parameter :: nmax = 100
    integer :: infile = 1, outfile = 2
    integer sizeX, sizeY, X(nmax), Y(nmax), Z(2 * nmax)
    integer :: sizeZ = 0, minvalue = 10000

    open(infile, file = 'inA.txt')
    open(outfile, file = 'out.txt')
    
    call inputArray(infile, X, sizeX)
    call inputArray(infile, Y, sizeY)
    
    call createArray(X, sizeX, Y, sizeY, Z, sizeZ)
    call getMinInArray(Z, sizeZ, minvalue)
    write(outfile, *) minvalue
    
    close(infile)
    close(outfile)
    
    contains
        subroutine inputArray(file, array, n)
            integer file, n, i
            integer array(nmax)
            read(file, *) n
            read(file, *) (array(i), i = 1, n)
        end subroutine inputArray

        subroutine outArray(file, array, col)
            integer file, col, i, j
            integer array(nmax)
            character(20) col_str
            write(col_str, *) col
            write(file, *) (array(j), j = 1, col)
        end subroutine outArray
        
        subroutine createArray(arrayX, sizeX, arrayY, sizeY, arrayZ, sizeZ)
            integer arrayX(nmax), arrayY(nmax), arrayZ(2 * nmax)
            integer sizeX, sizeY, sizeZ
            logical isContains
            
            do i = 1, sizeX
                isContains = .false.
                call inArray(arrayY, sizeY, arrayX(i), isContains)
                if (.not.isContains) then
                    sizeZ = sizeZ + 1
                    arrayZ(sizeZ) = arrayX(i)
                end if
            end do
            
            do i = 1, sizeY
                isContains = .false.
                call inArray(arrayX, sizeX, arrayY(i), isContains)
                if (.not.isContains) then ! .not. - отрицание
                    sizeZ = sizeZ + 1
                    arrayZ(sizeZ) = arrayY(i)
                end if
            end do
                    
        end subroutine createArray
        
        subroutine inArray(array, n, value, isContains) ! Подпрограмма будет проверять, входит ли элемент в массив. isContains - логическая переменная
            integer array(nmax)
            integer n, value, i
            logical isContains
            
            do i = 1, n
                if (value == array(i)) then
                    isContains = .true.
                    exit
                end if
            end do
        end subroutine inArray
        
        subroutine getMinInArray(array, n, m)
            integer n, i, m
            integer array(nmax)
            
            do i = 1, n
                if (array(i) < m .and. array(i) > 0) then
                    m = array(i)
                end if
            end do
        end subroutine getMinInArray
end program hehe
```
