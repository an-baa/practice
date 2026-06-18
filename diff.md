# Дифференцирование

## 1.Описание предметной области и сущностей:
Algebra: содержит реализацию правил дифференцирования, принимая исходное дерево выражений и решение о том, как именно его дифференцировать.
Expression: определяет общий контракт для всех элементов дерева.
ConstantExpression: числовое значение(константа), которое по правилам дифференцирования превращается в 0.
ParameterExpression: переменная, то бишь аргумент функции, по которому ведется дифференцирование.Преобразуентся в константу со значением 1.
BinaryExpression: составная сущность, инкапсулирует математические операции, требующие двух операндов(сложение, умножение).Содержит строгие структурные связи, ссылаясь на "левое" и "правое" подвыражения.
MethodCallExpression: составная сущность, которая представляет вызов сложных функций(таких, как Sin и Cos).Обработка этой сущности требует применения алгоритма дифференцирования для сложной функции(Chain Rule).
UnaryExpression: dспомогательная сущность.Обеспечивает целостность данных при неявном изменении типов(например, когда целое число должно восприниматься как вещественное).

## 2.Диаграмма классов:

```mermaid

classDiagram
    direction TB

    class Algebra {
        <<static>>
        +Differentiate(Expression function) Expression
        -BuildDerivativeTree(Expression currentExpr) Expression
        -ApplyChainRule(string targetMathMethod, Expression arg, Expression argDerivative, bool invertSign) Expression
    }

    class Expression {
        <<abstract>>
        +ExpressionType NodeType
        +Type Type
    }

    class ConstantExpression {
        +Object Value
    }

    class ParameterExpression {
        +String Name
    }

    class UnaryExpression {
        +Expression Operand
    }

    class BinaryExpression {
        +Expression Left
        +Expression Right
    }

    class MethodCallExpression {
        +MethodInfo Method
        +ReadOnlyCollection~Expression~ Arguments
    }

    Expression <|-- ConstantExpression : наследует базовые свойства 
    Expression <|-- ParameterExpression : наследует базовые свойства 
    Expression <|-- UnaryExpression : наследует базовые свойства
    Expression <|-- BinaryExpression : наследует базовые свойства 
    Expression <|-- MethodCallExpression : наследует базовые свойства 

    BinaryExpression --> Expression : оперирует левым поддеревом 
    BinaryExpression --> Expression : оперирует правым поддеревом 
    UnaryExpression --> Expression : содержит преобразуемый узел 
    MethodCallExpression --> Expression : хранит список аргументов 

    Algebra ..> Expression : принимает и возвращает в Differentiate()
    Algebra ..> BinaryExpression : разбирает операции сложения и умножения
    Algebra ..> MethodCallExpression : извлекает вызовы Math.Sin и Math.Cos
    Algebra ..> UnaryExpression : фильтрует узлы приведения типов(Convert)
```