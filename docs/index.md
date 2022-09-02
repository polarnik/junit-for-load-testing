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

# Применение __JUnit__ для нагрузки + параметризации и отладки тестов 

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
1. 📊 __BUILD_ID__ и его генерация при отладке
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

---

# Отладка

![bg height:75%](img/infra.team.png)

---

# Параметризация

![bg height:75%](img/infra.tools.png)

---

# Простые тесты без метрик

![bg 70%](img/trend.png)


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

<!-- _class: main -->

# __JUnit__ удобен для отладки, запуска, ... __JMeter Java-DSL__

---

<!-- _class: main -->

# __BUILD_ID__ для сравнения отчетов

---

![bg](img/compare.png)

---
# __4.__ 📊 __BUILD_ID__ и его генерация при отладке

- __Jenkins__ и результаты отдельного теста

- __Grafana__ и сравнение отчетов

- __JMeter__ и простановка __BUILD_ID__ в результатах теста

- __Gatling__ и простановка __BUILD_ID__ в результатах теста

- Автоматизация запуска тестов

---



<!-- _class: main -->

# __JUnit__ удобен для эмуляции __BUILD_ID__, даже при локальной отладке без __Jenkins__


---

<!-- _class: main -->

# Параметры теста как код

---

# __5.__ ⚙️ __@ParameterizedTest__ или параметры как код

---

<!-- _class: main -->

# Надежные и простые тесты на циклах и потоках

---

# __6.__ ⚙️ __@RepeatedTest__ для тестов масштабируемости

---

# __7.__ ⚙️ __@Execution(ExecutionMode.CONCURRENT)__

---

<!-- _class: main
_footer: Photo by <a href="https://unsplash.com/es/@nolanissac?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Nolan Issac</a> on <a href="https://unsplash.com/s/photos/coffee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  
-->

# Начни свой день с кофе


![bg](img/nolan-issac-It0DCaCBr40-unsplash.jpg)


---
<!-- _class: main
-->

# Начни свой тест с __JUnit__ !



---


# __JUnit__ для нагрузки: «Начни свой тест с __JUnit__!»




1. ⁉️ Чем __JUnit__ полезен нагрузочнику
1. 🔬 __Gatling__ и его отладка c __@Test__ 
1. 🔬 __JMeter-Java-DSL__ и его отладка c __@Test__ 
1. 📊 __BUILD_ID__ и его генерация при отладке
1. ⚙️ __@ParameterizedTest__ или параметры как код
1. ⚙️ __@RepeatedTest__ для тестов масштабируемости
1. ⚙️ __@Execution(ExecutionMode.CONCURRENT)__ для многопоточности

## Смирнов Вячеслав | ![h:35](themes/img/lead/miro.svg) Miro, ![h:35](themes/img/lead/Telegram_logo.svg) qa_load, ![h:35](themes/img/lead/Telegram_logo.svg) smirnovqa

### Repo: `github.com/polarnik/junit-for-load-testing`
