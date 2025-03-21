# Genomes Validator

Программа для оценки качества данных полногеномного секвенирования

## Содержание

* [Алгоритм](#algorithm)
* [Перечень микроорганизмов](#MicrobesList)
* [Запуск](#run)
* [Входные данные](#InputData)
* [Работа в режиме графического интерфейса](#GUI)
* [Трактовка результатов](#results)
* [Запуск в консольном режиме](#console)

## <a name="algorithm">Алгоритм</a>

Основной задачей программы «Genomes Validator» является оценка качества данных секвенирования на основе транслирования последовательностей генов жизнеобеспечения (housekeeping genes). Учитывая, что данные гены являются критически важными для существования микробной клетки, их отсутствие в геноме или критические ошибки трансляции (стоп-кодоны) расцениваются как ошибка секвенирования.  
Анализ проводится в три этапа. На первом этапе программа автоматически определяет вид анализируемого микроорганизма. На втором этапе проводится оценка общих (неспецифических) показателей, таких как общая длина генома, N50 и процент GC. На третьем этапе проводится поиск генов жизнеобеспечения, характерных для каждого из анализируемых видов, их трансляция и сравнение с референтной аминокислотной последовательностью.  

## <a name="MicrobesList">Перечень микроорганизмов</a>

Текущая версия программы позволяет проводить анализ данных полногеномного секвенирования следующих видов:

* Vibrio cholerae
* Francisella tularensis
* Klebsiella pneumoniae
* Pseudomonas aeruginosa 
* Yersinia enterocolitica
* Y. pestis
* Y. pseudotuberculosis
* Vibrio vulnificus

## <a name="run">Запуск</a>

Программа «Genomes Validator» является кроссплатформенной и предназначена для работы в операционных системах Windows, Linux и MacOS. Предусмотрен как графический интерфейс для пакетного режима работы с выводом результатов в общей таблице, так и консольный вариант запуска из командной строки с выводом результатов в json-формате.

Программа доступна в двух вариантах:

* Только исполняемый jar-файл, предназначенный для работы в среде Windows, Linux и MacOS. Требуется предварительная установка Java Runtime Environment (JRE).
* Полная версия с встроенной JRE. Предназначена для работы в среде Windows без необходимости установки дополнительных программ.

## <a name="InputData">Входные данные</a>

В качестве исходных данных используется файлы в fasta-формате, содержащие набор контигов, полученных в результате полногеномного секвенирования. Возможна пакетная обработка множества fasta-файлов (каждый файл содержит геном одного штамма)

## <a name="GUI">Работа в режиме графического интерфейса</a>

Внешний вид программы представлен ниже. Необходимо заполнить поле «Tilte» - эти данные будут использованы в качестве имени файла с результатом. 

![Внешний вид программы](img/GenomesValidator.jpg)

При нажатии на кнопку «Start» открывается стандартное диалоговое окно выбора исходного файла (файлов) для анализа. После выбора анализ начинается автоматически. В процессе работы внизу окна программы индикатор в виде тонкой бегущей полосы отображает прогресс анализа текущего файла. Если анализируется несколько геномов, индикатор в виде широкой бегущей полосы отображает общий прогресс анализа. Ориентировочное время работы программы от 1 до 5 минут для каждого штамма (fasta-файла).

После завершения работы программы результат сохраняется в текстовом файле, расположенном в папке с исходными fasta-файлами при этом имя файла совпадает с данными, введенными в поле «Title». При копировании этого файла в текстовый редактор (или MS Excel, LibreOffice Calc) данные отображаются в табличной форме. 

![Таблица - пример результата](img/Table.jpg)

*Примечание: в графе Reason указывается причина, по которой геном имеет среднее или плохое качество. Так у генома V-ch-18336 в гене pyrC обнаружен стоп-кодон, приводящий к образованию белка из 15 аминокислот вместо 342, что расценивается как ошибка сиквенса*

## <a name="results">Трактовка результатов</a>

В программе предусмотрена оценка качества данных секвенирования по трем градациям:

* Хорошее качество (good) – длина генома соответствует среднему размеру генома для вида +/- 20%, показатель N50 более 20 000, процент GC составляет +/- 2.5% от среднего для вида, все гены жизнеобеспечения найдены и трансляция их белкового продукта не нарушена стоп-кодоном.
* Среднее качество (average) – показатель N50 от 10 000 до 20 000 и все гены жизнеобеспечения найдены, но обнаружены ошибки их трансляции в следствии образования стоп-кодона из-за ошибки секвенирования.
* Плохое качество (bad) - N50 менее 10 000 либо не найден хотя бы один ген системы жизнеобеспечения, либо размер генома или процент GC не соответствует значению, характерному для вида

Геномы, имеющие «средний» уровень качества не рекомендуется использовать для филогенетического анализа, SNP-типирования или MLST-анализа, однако они могут быть использованы для поиска тех или иных генов (без их типирования) или INDEL-анализа.
Геномы, имеющие «плохой» уровень качества рекомендуются корректировать путем проведения повторного секвенирования.

## <a name="console">Запуск в консольном режиме</a>

Для запуска в консольном режиме на компьютере должен быть установлен Java Runtime Environment (JRE). Запуск осуществляется командой:

`java -jar GenomesValidator.jar  <genome.fasta>`

или 

`java -jar GenomesValidator.jar  <genome.fasta> <result file name>`

где:
* **genome.fasta** -– анализируемый геном в формате fasta,
* **result file name** -– имя файла с результатом. Если оно не задано, то будет использовано имя исходного файла с добавлением расширения json

Результат выводится в json-файл

```
{
    "File": "V-ch-18336.fasta",
    "Species": "V. cholerae",
    "Length": "4036549",
    "N50": "95054",
    "GC": "47,5",
    "Quality": "average",
    "Reason": "pyrC: 15/342 AK"
}
```





