# Migração da dependência do Groovy para o Kotlin DSL

Sempre tive vontade de escrever um artigo aqui no Linkedin e nesse primeiro artigo e nos próximos mostrarei sobre alguns assuntos que pode te ajudar a agregar conhecimento no desenvolvimento Android. Espero que este artigo lhe ajude a dar um passo a mais no seu progresso. Fique a vontade para dar sugestões e correções sobre esse e os próximos artigos.

Esse artigo descreverá sobre a migração da dependência padrão que o Android Studio utiliza (Atualmente ele utiliza a linguagem Groovy) para o Kotlin DSL. Se você está começando como desenvolvedor Android e deseja facilitar a utilização das dependências do seu projeto e utilizar apenas a linguagem Kotlin com certeza esse artigo lhe ajudará a dar esse primeiro passo. 

> Para esse artigo e nos próximos criei um novo projeto que será usado para as explicações dos artigos. 

## Etapa 1

Depois de criar o projeto, altere a visualização do Projeto Android para Projeto. 

## Etapa 2
Pressione o botão direito encima do seu projeto e selecione as opções New > Directory e nomeie para **buildSrc**. 

## Etapa 3
Pressione o botão direito encima do diretório buildSrc e selecione as opções New > File e nomeie para **build.gradle.kts**.

## Etapa 4
Dentro do arquivo **build.gradle.kts** digite os seguintes códigos:
```
plugins {
    `kotlin-dsl`
}
repositories {
    mavenCentral()
    google()
}
```
Após isso sincronize o seu projeto.

## Etapa 5
Abra os arquivos **build.gradle** do projeto e do módulo app e o arquivo **settings.gradle**.

> Para converter os arquivos do build gradle do Groovy para Kotlin basta renomear os três arquivos acima com o final kts. (**build.gradle.kts** e **settings.gradle.kts**). Nas próximas etapas vou demonstrar passo a passo as alterações entre o arquivo do build gradle do Groove para Kotlin.

## Etapa 6
Pressione o botão direito encima do arquivo settings.gradle e selecione as opções Refactor > Rename e renomeie para **settings.gradle.kts**.

Faça as seguintes alterações: 

###### Groovy
```
rootProject.name = "RickMortyAndroid"
include ':app'
```

###### Kotlin
```
rootProject.name = "RickMortyAndroid"
include(":app")
```

> Você vai notar alguns erros durante a migração mais não se preocupe. Durante as alterações que estamos fazendo aqui eles vão sumir.

## Etapa 7
Pressione o botão direito encima do arquivo build.gradle do projeto e selecione as opções Refactor > Rename e renomeie para **build.gradle.kts**.

Faça as seguintes alterações: 

###### Groovy
```
// Top-level build file where you can add configuration options common to all sub-projects/modules.
plugins {
    id 'com.android.application' version '8.0.0' apply false
    id 'com.android.library' version '8.0.0' apply false
    id 'org.jetbrains.kotlin.android' version '1.8.0' apply false
}
```

###### Kotlin
```
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    repositories {
        google()
        mavenCentral()
    }
    dependencies {
        classpath("com.android.tools.build:gradle:7.3.1")
        classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:1.7.20")
        classpath("com.android.tools.build:gradle:8.0.2")
    }
}
allprojects {
    repositories {
        google()
        mavenCentral()
    }
}

tasks.register("clean", Delete::class) {
    delete(rootProject.buildDir)
}
```

Após isso você pode sincronizar o projeto.

## Etapa 8
Pressione o botão direito encima do arquivo build.gradle do módulo app e selecione as opções Refactor > Rename e renomeie para **build.gradle.kts**.

Se uma mensagem de refatoração aparecer selecione a opção **Do Refactor**

Depois faça as seguintes alterações: 

###### Groovy
```
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

android {
    namespace 'br.com.rickmortyandroid'
    compileSdk 33

    defaultConfig {
        applicationId "br.com.rickmortyandroid"
        minSdk 25
        targetSdk 33
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
}

dependencies {

    implementation 'androidx.core:core-ktx:1.10.1'
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.9.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.5'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.5.1'
}
```

###### Kotlin
```
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
}

android {

    namespace = "br.com.rickmortyandroid"
    compileSdk = 33

    defaultConfig {
        applicationId = "br.com.rickmortyandroid"
        minSdk = 25
        targetSdk = 33
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        getByName("release") {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }

    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = "1.8"
    }
}

dependencies {
    implementation("androidx.core:core-ktx:1.10.1")
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.9.0")
    implementation("androidx.constraintlayout:constraintlayout:2.1.4")
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
}
```

Após isso você pode sincronizar o projeto.

Agora o seu projeto está pronto para usar o Kotlin DSL. Antes de terminar este artigo vou mostrar como eu separo as minhas dependências e as versões dela. Você pode utilizar objetos para armazenar os nomes e as versões das suas dependências. 

Para isso pressione o botão direito encima do diretório buildSrc e selecione as opções New > Directory e selecione a pasta **src/main/kotlin**.

Pressione o botão direito encima do diretório kotlin e selecione as opções New > Kotlin Class/File > Object e nomeie para Dependencies (Siga os mesmos passos e crie outro objeto chamado Version. Você pode criar em arquivos separados ou juntos. Fica ao seu critério).

Os dois objetos ficarão assim:

###### Dependencies
```
object Dependencies {
    const val core_ktx = "androidx.core:core-ktx:${Version.core_ktx_version}"
    const val appcompat = "androidx.appcompat:appcompat:${Version.appcompat_version}"
    const val material = "com.google.android.material:material:${Version.material_version}"
    const val constraintlayout =
        "androidx.constraintlayout:constraintlayout:${Version.constraintlayout_version}"
    const val junit = "junit:junit:${Version.junit_version}"
    const val junit_test_implementation =
        "androidx.test.ext:junit:${Version.junit_test_implementation_version}"
    const val espresso = "androidx.test.espresso:espresso-core:${Version.espresso_version}"
}
```
###### Version
```
object Version {
    const val core_ktx_version = "1.10.1"
    const val appcompat_version = "1.6.1"
    const val material_version = "1.9.0"
    const val constraintlayout_version = "2.1.4"
    const val junit_version = "4.13.2"
    const val junit_test_implementation_version = "1.1.5"
    const val espresso_version = "3.5.1"
}
```
No build.gradle do módulo app fica assim:
```
dependencies {
    implementation(Dependencies.core_ktx)
    implementation(Dependencies.appcompat)
    implementation(Dependencies.material)
    implementation(Dependencies.constraintlayout)
    testImplementation(Dependencies.junit)
    androidTestImplementation(Dependencies.junit_test_implementation)
    androidTestImplementation(Dependencies.espresso)
}
```
