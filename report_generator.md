# Генератор отчетов

## 1.Описание предметной области и сущностей:
Measurement: хранит запись о метеорологическом замере: температуру Temperature и влажность Humidity.
MeanAndStd: инкапсулирует вычисленное среднее значение Mean и стандартное отклонение Std, а также переопределяет метод ToString() для их корректного строкового представления.
ITextTemplate: содержит логику оформления отчетов, оперируя только строковым типом данных.Описывает контракт из четырех методов: создание заголовка, открытие списка, формирование строки данных, закрытие списка.
IEvalLogic: содержит алгоритмы статистической обработки.Принимает на вход набор чисел IEnumerable и возвращает результат object.
HtmlTemplate: реализует ITextTemplate, превращая данные в HTML-теги.
MdTemplate: реализует ITextTemplate, формируя отчет по правилам разметки Markdown.
MeanEval: реализует IEvalLogic.Обрабатывает массив чисел, вычисляя математическое ожидание и среднеквадратическое отклонение, возвращая объект MeanAndStd.
MedianEval: также реализует IEvalLogic.Сортирует массив чисел и находит медиану, возвращая число.
ReportMaker: координатор, хранито ссылки на интерфейсы ITextTemplate и IEvalLogic.Получает на вход коллекцию измерений Measurement и, извлекая конкретные показатели, передает их в вычислительную логику.
ReportMakerHelper: предоставляет настроенные сборки ReportMaker.

## 2.Диаграмма классов:

```mermaid

classDiagram
    class ITextTemplate {
        <<interface>>
        +WrapTitle(string title) string
        +Start() string
        +Row(string name, string val) string
        +End() string
    }

    class IEvalLogic {
        <<interface>>
        +Name string
        +Run(IEnumerable~double~ values) object
    }

    class HtmlTemplate {
        +WrapTitle(string title) string
        +Start() string
        +Row(string name, string val) string
        +End() string
    }
    ITextTemplate <|.. HtmlTemplate : разметка HTML

    class MdTemplate {
        +WrapTitle(string title) string
        +Start() string
        +Row(string name, string val) string
        +End() string
    }
    ITextTemplate <|.. MdTemplate : разметка Markdown

    class MeanEval {
        +Name string
        +Run(IEnumerable~double~ values) object
    }
    IEvalLogic <|.. MeanEval : расчет среднего и отклонения

    class MedianEval {
        +Name string
        +Run(IEnumerable~double~ values) object
    }
    IEvalLogic <|.. MedianEval : расчет медианы

    class Measurement {
        +Temperature double
        +Humidity double
    }

    class MeanAndStd {
        +Mean double
        +Std double
        +ToString() string
    }

    class ReportMaker {
        -ITextTemplate _view
        -IEvalLogic _calc
        +ReportMaker(ITextTemplate view, IEvalLogic calc)
        +MakeReport(IEnumerable~Measurement~ measurements) string
    }
    ReportMaker --> ITextTemplate : оформление текста(_view)
    ReportMaker --> IEvalLogic : расчет математики(_calc)
    ReportMaker ..> Measurement : извлекает свойства(Temperature/Humidity)

    class ReportMakerHelper {
        +MeanAndStdHtmlReport(IEnumerable~Measurement~ data) string
        +MedianMarkdownReport(IEnumerable~Measurement~ data) string
        +MeanAndStdMarkdownReport(IEnumerable~Measurement~ data) string
        +MedianHtmlReport(IEnumerable~Measurement~ data) string
    }
    ReportMakerHelper ..> ReportMaker : конструирует(new)
    ReportMakerHelper ..> HtmlTemplate : инициализирует для HTML-отчетов
    ReportMakerHelper ..> MdTemplate : инициализирует для MD-отчетов
    ReportMakerHelper ..> MeanEval : передает стратегию Mean
    ReportMakerHelper ..> MedianEval : передает стратегию Median
    
    MeanEval ..> MeanAndStd : создает экземпляр для возврата результатов
```