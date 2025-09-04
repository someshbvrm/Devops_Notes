
---

## 📌 Difference Between `pom.xml`, `build.gradle`, and `build.xml`

1. **Maven Project**

   * Uses **`pom.xml`** (Project Object Model).
   * If your repo has only `pom.xml`, it’s a **Maven project**.
   * Jenkins builds it with **Maven** (`mvn clean package`).

2. **Gradle Project**

   * Uses **`build.gradle`** or `build.gradle.kts`.
   * Does **not** need `pom.xml` or `build.xml`.
   * Jenkins builds it with **Gradle** (`gradle build` or `./gradlew build`).

3. **Ant Project**

   * Uses **`build.xml`**.
   * Does **not** need `pom.xml` or `build.gradle`.
   * Jenkins builds it with **Ant** (`ant jar`).

---

## ✅ Answer

* If you have **`pom.xml`** → it’s **Maven**, not Gradle, not Ant.
* If you want to use **Gradle**, you must have **`build.gradle`**.
* You **cannot build a Gradle project with only `pom.xml`**, and you **don’t need `build.xml`** unless using Ant.

---
