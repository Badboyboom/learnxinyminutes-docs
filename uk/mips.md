---
contributors:
  - ["Stanley Lim", "https://github.com/Spiderpig86"]
translators:
  - ["AstiaSun", "https://github.com/AstiaSun"]
---

Мова ассемблера MIPS (англ. Microprocessor without Interlocked Pipeline Stages) була написана для роботи з мікропроцесорами MIPS, парадигма яких була описана в 1981 році [Джоном Геннессі](https://uk.wikipedia.org/wiki/Джон_Лерой_Геннессі). Ці RISC процесори використовуються у таких вбудованих системах, як маршрутизатори та мережеві шлюзи.

[Детальніше](https://en.wikipedia.org/wiki/MIPS_architecture)

```asm
# Коментарі позначені як'#'

# Всі символи після '#' ігноруються лексичним аналізатором асемблера.

# Зазвичай програми поділяються на .data та .text частини

.data # У цьому розділі дані зберігаються у пам'яті, виділеній в RAM, подібно до змінних
	   # в мовах програмування вищого рівня

  # Змінна оголошується наступним чином: [назва]: .[тип] [значення] 
  # Наприклад:
  hello_world: .asciiz "Hello World\n"      # Оголосити текстову змінну
  num1: .word 42                            # word - це чисельний тип 32-бітного розряду

  arr1: .word 1, 2, 3, 4, 5                 # Масив чисел
  arr2: .byte 'a', 'b'                      # Масив буквених символів (розмір кожного - 1 байт)
  buffer: .space 60                         # Виділити місце в RAM 
                                            # (не очищується, тобто не заповнюється 0)

  # Розміри типів даних
  _byte: .byte 'a'                          # 1 байт
  _halfword: .half 53                       # 2 байти
  _word: .word 3                            # 4 байти
  _float: .float 3.14                       # 4 байти
  _double: .double 7.0                      # 8 байтів

  .align 2                                  # Вирівнювання пам'яті даних, де число
                                            # показує кількість байтів, вирівнених 
                                            # у степені 2. (.align 2 означає 
                                            # чисельне (word) вирівнювання оскільки 
                                            # 2^2 = 4 байти)

.text                                       # Розділ, що містить інструкції та
                                            # логіку програми

.globl _main                                # Оголошує назву інструкції як 
                                            # глобальну, тобто, яка є доступною для
                                            # всіх інших файлів

  _main:                                    # програми MIPS виконують інструкції
                                            # послідовно, тобто першочергово код
                                            # буде виконуватись після цієї позначки

    # Виведемо на екран "hello world"
    la $a0, hello_world                     # Завантажує адресу тексту у пам'яті
    li $v0, 4                               # Завантажує значення системної
                                            # команди (вказуючи тип функціоналу)
    syscall                                 # Виконує зазначену системну команду
                                            # з обраним аргументом ($a0)

    # Регістри (використовуються, щоб тримати дані протягом виконання програми)
    # $t0 - $t9                             # Тимчасові регістри використовуються
                                            # для проміжних обчислень всередині
                                            # підпрограм (не зберігаються між 
                                            # викликами функцій)

    # $s0 - $s7                             # Збережені регістри, у яких значення 
                                            # зберігаються між викликами підпрограм.
                                            # Зазвичай зберігаються у стеку.

    # $a0 - $a3                             # Регістри для передачі аргументів для 
                                            # підпрограм
    # $v0 - $v1                             # Регістри для значень, що повертаються
                                            # від викликаної функції

    # Типи інструкції завантаження / збереження
    la $t0, label                           # Скопіювати адресу в пам'яті, де 
                                            # зберігається значення змінної label 
                                            # в регістр $t0
    lw $t0, label                           # Скопіювати чисельне значення з пам'яті
    lw $t1, 4($s0)                          # Скопіювати чисельне значення з адреси
                                            # пам'яті регістра зі зміщенням в 
                                            # 4 байти (адреса + 4)
    lb $t2, label                           # Скопіювати буквений символ в частину
                                            # нижчого порядку регістра $t2
    lb $t2, 0($s0)                          # Скопіювати буквений символ з адреси
                                            # в $s0 із зсувом 0
    # Подібне використання і 'lh' для halfwords

    sw $t0, label                           # Зберегти чисельне значення в адресу в 
                                            # пам'яті, що відповідає змінній label
    sw $t0, 8($s0)                          # Зберегти чисельне значення в адресу, 
                                            # що зазначена у $s0, та зі зсувом у 8 байтів
    # Така ж ідея використання 'sb' та 'sh' для буквених символів та halfwords. 
    # 'sa' не існує
    
    
### Математичні операції ###
  _math:
    # Пам'ятаємо, що попередньо потрібно завантажити дані в пам'ять
    lw $t0, num                             # Із розділа з даними
    li $t0, 5                               # Або безпосередньо з константи
    li $t1, 6
    add $t2, $t0, $t1                       # $t2 = $t0 + $t1
    sub $t2, $t0, $t1                       # $t2 = $t0 - $t1
    mul $t2, $t0, $t1                       # $t2 = $t0 * $t1
    div $t2, $t0, $t1                       # $t2 = $t0 / $t1 (Може не підтримуватись
                                            # деякими версіями MARS)
    div $t0, $t1                            # Виконує $t0 / $t1. Отримати частку можна
                                            # за допомогою команди 'mflo', остаток - 'mfhi'

    # Бітовий зсув
    sll $t0, $t0, 2                         # Побітовий зсув вліво на 2. Біти вищого порядку
                                            # не зберігаються, нищого - заповнюються 0
    sllv $t0, $t1, $t2                      # Зсув вліво зі змінною кількістю у
                                            # регістрі
    srl $t0, $t0, 5                         # Побітовий зсув вправо на 5 (не зберігає 
                                            # біти, біти зліва заповнюються 0)
    srlv $t0, $t1, $t2                      # Зсув вправо зі змінною кількістю у
                                            # регістрі
    sra $t0, $t0, 7                         # Побітовий арифметичний зсув вправо 
                                            # (зберігає біти)
    srav $t0, $t1, $t2                      # Зсув вправо зі змінною кількістю у 
                                            # регістрі зі збереження значеннь бітів

    # Побітові операції
    and $t0, $t1, $t2                       # Побітове І (AND)
    andi $t0, $t1, 0xFFF                    # Побітове І з безпосереднім значенням
    or $t0, $t1, $t2                        # Побітове АБО (OR)
    ori $t0, $t1, 0xFFF                     # Побітове АБО з безпосереднім значенням
    xor $t0, $t1, $t2                       # Побітова виключна диз'юнкція (XOR)
    xori $t0, $t1, 0xFFF                    # Побітове XOR з безпосереднім значенням
    nor $t0, $t1, $t2                       # Побітова стрілка Пірса (NOR)

## Розгалуження ##
  _branching:
    # В основному інструкції розгалуження мають наступну форму:
    # <instr> <reg1> <reg2> <label>
    # де label - це назва змінної, в яку ми хочемо перейти, якщо зазначене твердження
    # правдиве

    beq $t0, $t1, reg_eq                    # Перейдемо у розгалуження reg_eq
                                            # якщо $t0 == $t1, інакше -
                                            # виконати наступний рядок
    bne $t0, $t1, reg_neq                   # Розгалужується, якщо $t0 != $t1
    b branch_target                         # Розгалуження без умови завжди виконується
    beqz $t0, req_eq_zero                   # Розгалужується, якщо $t0 == 0
    bnez $t0, req_neq_zero                  # Розгалужується, якщо $t0 != 0
    bgt $t0, $t1, t0_gt_t1                  # Розгалужується, якщо $t0 > $t1
    bge $t0, $t1, t0_gte_t1                 # Розгалужується, якщо $t0 >= $t1
    bgtz $t0, t0_gt0                        # Розгалужується, якщо $t0 > 0
    blt $t0, $t1, t0_gt_t1                  # Розгалужується, якщо $t0 < $t1
    ble $t0, $t1, t0_gte_t1                 # Розгалужується, якщо $t0 <= $t1
    bltz $t0, t0_lt0                        # Розгалужується, якщо $t0 < 0
    slt $s0, $t0, $t1                       # Інструкція, що посилає сигнал коли
                                            # $t0 < $t1, результат зберігається в $s0 
                                            # (1 - правдиве твердження)

    # Просте твердження якщо (if)
    # if (i == j)
    #     f = g + h;
    #  f = f - i;

    # Нехай $s0 = f, $s1 = g, $s2 = h, $s3 = i, $s4 = j
    bne $s3, $s4, L1 # if (i !=j)
    add $s0, $s1, $s2 # f = g + h

    L1:
      sub $s0, $s0, $s3 # f = f - i
    
    # Нижче наведений приклад знаходження максимального значення з 3 чисел
    # Пряма трансляція в Java з логіки MIPS:
    # if (a > b)
    #   if (a > c)
    #     max = a;
    #   else
    #     max = c;
    # else
    #     max = b;
    #   else
    #     max = c;

    # Нехай $s0 = a, $s1 = b, $s2 = c, $v0 = повернути регістр
    ble $s0, $s1, a_LTE_b                   # якщо (a <= b) розгалуження(a_LTE_b)
    ble $s0, $s2, max_C                     # якщо (a > b && a <=c) розгалуження(max_C)
    move $v0, $s0                           # інакше [a > b && a > c] max = a
    j done                                  # Перейти в кінець програми

    a_LTE_b:                                # Мітка розгалуження, коли a <= b
      ble $s1, $s2, max_C                   # якщо (a <= b && b <= c) розгалуження(max_C)
      move $v0, $s1                         # якщо (a <= b && b > c) max = b
      j done                                # Перейти в кінець програми

    max_C:
      move $v0, $s2                         # max = c

    done:                                   # Кінець програми

## Цикли ##
  _loops:
    # Цикл складається з умови виходу та з інструкції переходу після його завершення
    li $t0, 0
    while:
      bgt $t0, 10, end_while                # Коли $t0 менше 10, продовжувати ітерації
      addi $t0, $t0, 1                      # Збільшити значення
      j while                               # Перейти на початок циклу
    end_while:

    # Транспонування 2D матриці
    # Припустимо, що $a0 зберігає адресу цілочисельної матриці розмірністю 3 x 3
    li $t0, 0                               # Лічильник для i
    li $t1, 0                               # Лічильник для j
    matrix_row:
      bgt $t0, 3, matrix_row_end

      matrix_col:
        bgt $t1, 3, matrix_col_end

        # ...

        addi $t1, $t1, 1                  # Збільшити лічильник стовпця (col)
      matrix_col_end:

      # ...

      addi $t0, $t0, 1
    matrix_row_end:

## Функції ##
  _functions:
    # Функції - це процедури, що викликаються, приймають аргументи та повертають значення

    main:                                 # Програма починається з головної функції
      jal return_1                        # jal збереже поточний ПЦ (програмний центр) в $ra,
                                          # а потім перейде до return_1

      # Як передати аргументи?
      # По-перше, ми маємо передати значення аргументів у регістри аргументів
      li $a0, 1
      li $a1, 2
      jal sum                             # Тепер ми можемо викликати функцію

      # Як щодо рекурсії?
      # Тут потрібно дещо більше роботи оскільки ми маємо впевнитись, що ми збережемо
      # та зчитаємо попередній ПЦ в $ra, оскільки jal автоматично перепише її при виклику
      li $a0, 3
      jal fact

      li $v0, 10
      syscall
    
    # Ця функція повертає 1
    return_1:
      li $v0, 1                           # Завантажити val в регіст $v0
      jr $ra                              # Повернутись до попереднього ПЦ і продовжити виконання 


    # Функція з двома аргументами
    sum:
      add $v0, $a0, $a1
      jr $ra                              # Повернутись

    # Рекурсивна функція, яка знаходить факторіал
    fact:
      addi $sp, $sp, -8                   # Виділити місце в стеку
      sw $s0, ($sp)                       # Зберегти регістр, що містить поточне число
      sw $ra, 4($sp)                      # Зберегти попередній ПЦ

      li $v0, 1                           # Проініціалізувати значення, що повертатиметься
      beq $a0, 0, fact_done               # Закінчити, якщо параметр 0

      # Інакше, продовжити рекурсію
      move $s0, $a0                       # Скопіювати $a0 в $s0
      sub $a0, $a0, 1
      jal fact

      mul $v0, $s0, $v0                   # Множення

      fact_done:
        lw $s0, ($sp)
        lw $ra, ($sp)                     # Відновити ПЦ
        addi $sp, $sp, 8

        jr $ra

## Макроси ##
  _macros:
    # Макроси надзвичайно корисні для заміни блоків коду, що повторюються, за допомогою
    # однієї змінної, для покращення читабельності
    # Це не заміна функцій.
    # Вони мають бути оголошені перед використанням

    # Макрос для виведення нових рядків (оскільки операція досить часто виконується)
    .macro println()
      la $a0, newline                     # Значення нового рядка зберігатиметься тут
      li $v0, 4
      syscall
    .end_macro

    println()                             # Асемблер скопіює цей блок коду сюди
                                          # перед тим, як виконувати його

    # Можна передавати параметри у макроси.
    # Параметри позначаються знаком '%' з довільною назвою
    .macro print_int(%num)
      li $v0, 1
      lw $a0, %num
      syscall
    .end_macro
    
    li $t0, 1
    print_int($t0)
    
    # Значення також можна передавати безпосередньо в макроси
    .macro immediates(%a, %b)
      add $t0, %a, %b
    .end_macro

    immediates(3, 5)

    # Одночасно із назвами змінних
    .macro print(%string)
      la $a0, %string
      li $v0, 4
      syscall
    .end_macro

    print(hello_world)

## Масиви ##
.data
  list: .word 3, 0, 1, 2, 6                 # Це масив чисел
  char_arr: .asciiz "hello"                 # Це текстовий масив
  buffer: .space 128                        # Виділяє блок пам'яті, що
                                            # автоматично не очищується
                                            # Ці блоки пам'яті вирівнені
                                            # вирівнені поруч один з одним

.text
  la $s0, list                              # Завантажити адресу списку
  li $t0, 0                                 # Лічильник
  li $t1, 5                                 # Довжина списку

  loop:
    bgt $t0, $t1, end_loop

    lw $a0, ($s0)
    li $v0, 1
    syscall                                 # Вивести число

    addi $s0, $s0, 4                        # Розмір числа - 4 байти
    addi $t0, $t0, 1                        # Збільшити
    j loop
  end_loop:

## Включення ##
# Потрібно для імпорту сторонніх файлів у програму (насправді, код з цього файлу 
# копіюється та вставляється в місце, де оголошений імпорт)
.include "somefile.asm"
```
