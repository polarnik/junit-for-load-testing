---
marp: true
title: JUnit для нагрузки
description: Про опыт применения JUnit для написания тестов производительности, а также для отладки и параметризации 
theme: tdconf
template: bespoke
paginate: true
_paginate: false

---

<!-- _class: lead
-->

# JUnit для нагрузки

## Смирнов Вячеслав

### ![h:55](themes/img/lead/miro.svg) Miro 

<!--
_footer: Image by Vlad Gerasimov on <a href="https://vlad.studio/">vlad.studio</a>
 -->


---
<!-- _class: title -->

# Инженер ![h:55](themes/img/lead/miro.svg) Miro
## Развиваю ![h:55](themes/img/lead/Telegram_logo.svg) qa_load

![bg cover](img/omsk.jpg)

<!-- 
Повышаю качество более десяти лет. Занимаюсь системой дистанционного банковского обслуживания юридических лиц. Основной профиль моей работы — тестирование производительности. Развиваю сообщество инженеров по тестированию производительности, помогая коллегам в telegram чате «QA — Load & Performance».
-->

---
<!-- _class: main -->

# Применим __JUnit__ для профессиональной отладки тестов 

<!--
_footer: Image by Vlad Gerasimov on <a href="https://vlad.studio/">vlad.studio</a>
 -->

---

<!--
_footer: Photo by <a href="https://unsplash.com/es/@nolanissac?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Nolan Issac</a> on <a href="https://unsplash.com/s/photos/coffee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  
 -->

![bg](img/nolan-issac-It0DCaCBr40-unsplash.jpg)

---
<!-- _class: main -->

# ![h:240](img/junit4.png )  ![h:300](img/junit5-logo.svg) ![h:300](img/junit5-logo.png)

---
# Содержание

1. ⁉️ Чем __JUnit__ полезен нагрузочнику

1. 🔬 __Gatling__ и его отладка c __@Test__ 
1. 🔬 __JMeter-Java-DSL__ и его отладка c __@Test__ 
1. 📊 __Start__/__Stop__-time и их фиксация при отладке
1. 📊 __TestId__/__RunId__/__*Id__ и его генерация при отладке
1. ⚙️ __@ParameterizedTest__ или параметры как код
1. ⚙️ __@RepeatedTest__ для тестов масштабируемости
1. ⚙️ __@Execution(ExecutionMode.CONCURRENT)__ для многопоточности


---

<!-- _class: main -->

# Чем __JUnit__ полезен нагрузочнику

---
# __1.__ ⁉️ Чем __JUnit__ полезен нагрузочнику

- Реализация подхода __«всё как код»__

- Отладка
- Параметризация
- Простые тесты без метрик


---

# Реализация подхода __«всё как код»__

![bg cover](img/miro.png)

- тесты, как код  — __JMeter-java-dsl__, __Gatling__

- тестовые данные, как код — __@Parametrized__
- параметры отладки, как код — __@Test__, __@IntelliJ IDEA__, __JUnit Plugin__
- библиотеки и версии, как код  — __Maven__, __Laconic POM for Maven__
- презентация по __JUnit__, как код — __Visual Studio Code__, __Marp.App Extension__

---

# Отладка

![bg height:75%](img/infra.team.png)

## Сокращение времени на реализацию сложных тестов
## Тестирование сложных и новых систем
## Рост сложности задач
### Рост зарплаты

---

# Параметризация


---

# Простые тесты без метрик

## Подача нагрузки Unit-тестами

---

<!-- _class: main -->

# __«Всё как код»__ и отладка




---
# __2.__ 🔬 __Gatling__ и его отладка c __@Test__ 

- Отладка для __gatling-java-dsl__

- Отладка для __gatling-scala-dsl__

---

# Отладка для __gatling-java-dsl__

```java
    import io.gatling.app.Gatling;
    import org.testng.annotations.Test;
    import scala.collection.mutable.HashMap;
    public class DebugTest {
        var config = new HashMap<String, String>();
        void runSimulation(String simulationClass) {
            config.put("gatling.core.simulationClass", 
                simulationClass);
            Gatling.fromMap(config);
        }
        @Test void debugMaxPerfSimulation() {
            runSimulation("simulation.MaxPerf");
        }
    }
```
---


# __HashMap__ с настройками и метод запуска симуляции

```java
//  import io.gatling.app.Gatling;
//  import org.testng.annotations.Test;
//  import scala.collection.mutable.HashMap;
//  public class DebugTest {
        var config = new HashMap<String, String>();
        void runSimulation(String simulationClass) {
            config.put("gatling.core.simulationClass", 
                simulationClass);
            Gatling.fromMap(config);
        }
//      @Test void debugMaxPerfSimulation() {
//          runSimulation("simulation.MaxPerf");
//      }
//  }
```

---

# __@Test__, запускающий симуляцию

```java
//  import io.gatling.app.Gatling;
//  import org.testng.annotations.Test;
//  import scala.collection.mutable.HashMap;
//  public class DebugTest {
//      var config = new HashMap<String, String>();
//      void runSimulation(String simulationClass) {
//          config.put("gatling.core.simulationClass", 
//              simulationClass);
//          Gatling.fromMap(config);
//      }
        @Test void debugMaxPerfSimulation() {
            runSimulation("simulation.MaxPerf");
        }
//  }
```

---
# Ставим точку останова 🔴 и запускаем отладку 🪲

```java
    import io.gatling.app.Gatling;
    import org.testng.annotations.Test;
    import scala.collection.mutable.HashMap;
    public class DebugTest {
        var config = new HashMap<String, String>();
        void runSimulation(String simulationClass) {
            config.put("gatling.core.simulationClass", 
                simulationClass);
            Gatling.fromMap(config);
        }
🪲      @Test void debugMaxPerfSimulation() {
🔴          runSimulation("simulation.MaxPerf");
        }
    }
```


---

# Запускаем разные симуляции через __@Test__

```java
@Test void maxPerfSimulation() {
    runSimulation("simulation.MaxPerf");
}

@Test void stableSimulation() {
    runSimulation("simulation.Stable");
}

@Test void onceOnlySimulation() {
    runSimulation("simulation.OnceOnly");
}
```

---

# Генерируем html-отчеты через __@Test__

```java
@Test void generateReport() {
    HashMap<String, String> configLocal = new HashMap<>();
    {
        configLocal.put("gatling.charting.maxPlotPerSeries", 
          "600");
        configLocal.put("gatling.core.directory.reportsOnly", 
          "gatling/maxperfsimulation-20220321094726824");
    }
    Gatling.fromMap(configLocal);
}
```


---

<!-- _class: main -->

# __JUnit__ удобен для отладки, запуска, ... __Gatling Java-DSL__


---

# Отладка для __gatling-scala-dsl__

```scala
    import io.gatling.app.Gatling
    import io.gatling.core.ConfigKeys.{core, data}

    object DebugApp extends App {

      val config = scala.collection.mutable.Map(
        core.SimulationClass ->  "simulation.MaxPerf"  
        )

      Gatling.fromMap(config)
    }
```

---

# Ставим точку останова 🔴 и запускаем отладку 🪲

```scala
    import io.gatling.app.Gatling
    import io.gatling.core.ConfigKeys.{core, data}

🪲  object DebugApp extends App {

      val config = scala.collection.mutable.Map(
        core.SimulationClass ->  "simulation.MaxPerf"  
        )

🔴    Gatling.fromMap(config)
    }
```

---

# Несколько симуляций в одном классе c __@Test__

```scala
    import io.gatling.app.Gatling
    import io.gatling.core.ConfigKeys.{core, data}
    import org.junit.Test
    class DebugTest {
      var config = scala.collection.mutable.Map()
🪲    @Test def maxPerfSimulation() {
        config.put(core.SimulationClass, "simulation.MaxPerf")
        Gatling.fromMap(config);
      }
🪲    @Test def stableSimulation() {
        config.put(core.SimulationClass, "simulation.Stable")
        Gatling.fromMap(config);
      }
    }
```

---

<!-- _class: main -->

# __JUnit__ удобен для отладки, запуска, ... __Gatling Scala-DSL__


---
# __3.__ 🔬 __JMeter-Java-DSL__ и его отладка c __@Test__ 

- Отладка для __jmeter-java-dsl__

- Отладка для lambda-блоков __`jsr223Sampler(v -> {...})`__

---

# Отладка для __jmeter-java-dsl__ (by desing)

```java
    public class ZeroTest {
        @Test public void zero() throws IOException {
            TestPlanStats stats = testPlan(
                threadGroup(1, 1,
                    jsr223Sampler("zero", v -> {
                        v.log.info("Hello World!");
                    })
                )
            ).run();
            assertThat(stats.overall().sampleTimePercentile99())
                .isLessThan(Duration.ofSeconds(5));
        }
    }
```

---

# Ставим точку останова 🔴 и запускаем отладку 🪲

```java
    public class ZeroTest {
🪲      @Test public void zero() throws IOException {
🔴          TestPlanStats stats = testPlan(
🔴              threadGroup(1, 1,
🔴                  jsr223Sampler("zero", v -> {
                        v.log.info("Hello World!");
🔴                  })
🔴              )
🔴          ).run();
🔴          assertThat(stats.overall().sampleTimePercentile99())
🔴              .isLessThan(Duration.ofSeconds(5));
        }
    }
```

---

# __Lambda-выражения__ отлаживать нельзя 💔

```java
    public class ZeroTest {
🪲      @Test public void zero() throws IOException {
            TestPlanStats stats = testPlan(
                threadGroup(1, 1,
                    jsr223Sampler("zero", v -> {
💔                      v.log.info("Hello World!");
                    })
                )
            ).run();
            assertThat(stats.overall().sampleTimePercentile99())
                .isLessThan(Duration.ofSeconds(5));
        }
    }
```

---
# Но можно создать вспомогательные классы 😎
### Создать в них конструкторы и ссылки на все нужное
```java
public class AbstractScenario {
    public AbstractScenario AbstractScenario(
        DslJsr223Sampler.SamplerVars v) {
        this.samplerVars = v;
        return this.setLog(v.log)
                   .setCtx(v.ctx)
                   .setVars(v.vars)
                   .setProps(v.props)
                   .setSampleResult(v.sampleResult);
    }
    ... 
}
```

---
# И писать методы классов 
```java
    public class SomeScenario extends AbstractScenario {
        int errorCount = 0;
        public boolean helloWorld() {
            this.log.info("Hello World!");
        }
    }
```

---
# __Легко отлаживаемые__ методы классов 

```java
    public class SomeScenario extends AbstractScenario {
        int errorCount = 0;
        public boolean helloWorld() {
🔴          this.log.info("Hello World!");
        }
    }
```

---
# Которые вызывать в __lambda-выражениях__

```java
    public class SomeScenario extends AbstractScenario {
        int errorCount = 0;
        public boolean helloWorld() {
🔴          this.log.info("Hello World!");
⬆️      }
⬆️  }
    public class ZeroTest {
🪲      @Test public void zero() throws IOException {
            TestPlanStats stats = testPlan(
⬆️              threadGroup(1, 1,
⬆️                  jsr223Sampler("zero", v -> {
⬆️  ⬅️  ⬅️  ⬅️  ⬅️      new SomeScenario(v).helloWorld();
                    }))).run();
        }   
    }
```
---

<!-- _class: main -->

# __JUnit__ удобен для отладки, запуска, ... __JMeter Java-DSL__

---

<!-- _class: main -->

# __TestId__/__RunId__/__*Id__ или __Start__+__StopTime__ для сравнения тестов

---
# __4.__ 📊 __Start__/__Stop__-time и их фиксация при отладке

- __Grafana__ и сравнение метрик из __Prometheus__, __Victoria__, __InfluxDB__

- __Jenkins__ и фиксация моментов старта и завершения теста

- __JUnit__ для фиксации __Start__/__Stop__-time при отладке

---

# __Grafana__ и сравнение метрик __Prometheus__, __InfluxDB__, ...

![bg](img/compare.png)

---

# __Grafana__ и сравнение метрик __Prometheus__, __InfluxDB__, ...

```java
1. Отобразить таблицу по первому тесту:
  - "Start" и "Stop" - формат unixTimeStamp, тип "tag"/"label"
2. При клике по строке первой таблицы сохраняем в URL: 
  - from="Start", to="Stop", Start1="Start"
3. Отобразить таблицу по второму тесту
  - "Start" и "Stop" - формат unixTimeStamp, тип "tag"/"label"
  - "Start1" - дополнительная колонка из переменной "Start1"
  - "Offset" - вычисляемая колонка "Start1"-"Start"
4. При клике по строке второй таблицы сохраняем в URL: 
  - "Offset"="Offset"
5. Отобразить метрики по первому и второму тестам
  - по первому просто от "from" до "to"
  - по второму со смещением "Offset"
```

---

# Самое главное: __Start__ и __Stop__, тип __tag__/__label__

```java
1. Отобразить таблицу по первому тесту:
  - "Start" и "Stop", тип "tag"/"label", формат unixTimeStamp
2. При клике по строке первой таблицы сохраняем в URL: 
  - from=Start, to=Stop, Start1=Start
3. Отобразить таблицу по второму тесту
  - "Start" и "Stop", тип "tag"/"label", формат unixTimeStamp
  - Start1 - дополнительная колонка из переменной Start1
  - Offset - вычисляемая колонка Start1-Start
4. При клике по строке второй таблицы сохраняем в URL: 
  - Offset=Offset
5. Отобразить метрики по первому и второму тестам
  - по первому просто от from до to
  - по второму со смещением Offset
```

---

<!-- _class: main -->

# Как сохранить __Start__/__Stop__-time для каождого запуска теста?

---

# __Jenkins__ и фиксация моментов старта и завершения

## До теста: сохранить __Start__ и расчётный __Stop__-time
- как __tag__/__label__

## Тест: ab, curl+bash, k6, jmeter, locust, gatling, junit, ...
- любой тест!

## После теста: сохранить __Start__ и __Stop__-time фактические
 - как __tag__/__label__ 

---

# __Jenkins__ и фиксация моментов старта и завершения

## До теста: сохранить __Start__ и расчётный __Stop__-time
- как __tag__/__label__ в формате unixTimeStamp для UTC

## Тест: ab, curl+bash, k6, jmeter, locust, gatling, junit, ...
- любой тест, без метрик, распределенный, нестабильный, ...

## После теста: сохранить __Start__ и __Stop__-time фактические
 - как __tag__/__label__ в формате unixTimeStamp для UTC
 - +value __Duration__ или любое другое
 - +tag/label для __ServerVersion__, __Environment__, __PipelineName__, __Scenario__

---
# Пример записи метрик в формате __InfluxLine__ в __VM__

```python
metricTime = 1640980800 # Fri Dec 31 2021 20:00:00 GMT+0000
reqBody = f'testStats,' \
        f'suite={row["suite"]},' \
        f'environment={row["env"]},' \
        f'version={row["version"]},' \
        f'start={row["startUnix"]},' \
        f'stop={row["stopUnix"]} ' \
        f'duration={row["duration"]} {metricTime}000000000'

http.post(
    url="http://victoriaMetrics:8428/write",
    data=reqBody
)
```

---
# __5.__ 📊 __TestId__/__RunId__/__*Id__ и его генерация при отладке

- __Jenkins__ и результаты отдельного теста

- __JMeter__ и простановка __BUILD_ID__ в результатах теста

- __Gatling__ и простановка __BUILD_ID__ в результатах теста

- __InfluxDB__ или __Prometheus__ для аггрегации результатов теста

- __JUnit__ для генерации __TestId__/__RunId__/__*Id__ при отладке

---



<!-- _class: main -->

# __JUnit__ удобен для эмуляции __TestId__/__RunId__/__*Id__ при  отладке без __CI__


---

<!-- _class: main -->

# Параметры теста как код

---

# __6.__ ⚙️ __@ParameterizedTest__ или параметры как код

---

<!-- _class: main -->

# Надежные и простые тесты на циклах и потоках

---

# __7.__ ⚙️ __@RepeatedTest__ для тестов масштабируемости

---

# __8.__ ⚙️ __@Execution(ExecutionMode.CONCURRENT)__

---

<!-- _class: main
_footer: Photo by <a href="https://unsplash.com/es/@nolanissac?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Nolan Issac</a> on <a href="https://unsplash.com/s/photos/coffee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  
-->

# Начни свой день с кофе


![bg opacity:0.5](img/nolan-issac-It0DCaCBr40-unsplash.jpg)


---
<!-- _class: main
-->

# Начни свой тест с __JUnit__ !



---


# __JUnit__ для нагрузки: «Начни свой тест с __JUnit__!»




1. ⁉️ Чем __JUnit__ полезен нагрузочнику
1. 🔬 __Gatling__ и его отладка c __@Test__ 
1. 🔬 __JMeter-Java-DSL__ и его отладка c __@Test__ 
1. 📊 __Start__/__Stop__-time и их фиксация при отладке
1. 📊 __TestId__/__RunId__/__*Id__ и его генерация при отладке
1. ⚙️ __@ParameterizedTest__ или параметры как код
1. ⚙️ __@RepeatedTest__ для тестов масштабируемости
1. ⚙️ __@Execution(ExecutionMode.CONCURRENT)__ для многопоточности

## Смирнов Вячеслав | ![h:35](themes/img/lead/miro.svg) Miro, ![h:35](themes/img/lead/Telegram_logo.svg) qa_load, ![h:35](themes/img/lead/Telegram_logo.svg) smirnovqa

### 🔗 `polarnik.github.io/junit-for-load-testing/`


---

