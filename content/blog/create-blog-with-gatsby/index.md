---
title: Как создать простой блог на Gatsby
date: "2015-05-01T22:12:03.284Z"
description: "Создаем блог свой блог с помощью Gatsby"
---

И так. Я решил завести себе блог. И первым делом у меня возник вопрос - какой движок выбрать(Нет что бы что-нибудь путнее писать - он движок выбирает😱 Ясень пень, ведь в блоге это самое важное...)?
Пошарив чуть-чуть по просторам пришел к мнению, что все просто кипятком писают от Gatsby. И мне он, в общем то, то же понравился. Начнем.

Что такое Gatsby можно прочитать [тут](https://www.gatsbyjs.org/docs/).

Запилим свой блог:

## Шаг 1
## Установка npm.

Я использую npm с nvm. Это очень удобно когда у тебя много проектов, запущенных в разное время. Как установить nvm можно прочитать в [этой статье](/how-install-nvm/) или в документации https://github.com/nvm-sh/nvm#install--update-script. В

## Шаг 2
## Установка Gatsby

Чтобы использовать кодогенерацию на основе шаблонов, нам нужно установить Gatsby глобально. Как это сделать написано [здесь](https://www.gatsbyjs.org/tutorial/part-zero/), но если кратко, то делаем так:

```bash
npm install -g gatsby-cli
```

## Шаг 3
## Создание проекта

И так, воссияем же во имя Юпитера. В смысле создадим наш блог. Одним из приемуществ Gatsby является возможность генерировать проект из шаблонов. И для блога есть хорошоий шаблон https://github.com/gatsbyjs/gatsby-starter-blog.

Фиганем:

```bash
gatsby new my-awesome-blog https://github.com/gatsbyjs/gatsby-starter-blog
```

## Шаг 4
## Запустим.

Переходим в папку с нашим новым блогом и запустим его, что бы отслеживать что мы делаем с контентом.

```bash
npm run develop
```

Переходим в браузер по адресу http://localhost:8000 и смотрим на наш новый блог. Только вот, заголовок у него не наш, и мужик на фото какой-то не знакомый... Исправим это.

## Шаг 5
## Знакомимся с блогом

Если мы посмотрим в папку с нашим блогом, то заметим много каких-то файлов и папок. Если Вы часто разрабатываете приложение на ```React```, то для Вас всё будет знакомым. Но в кратце так:

* node_modules - Папка, в которой лежат зависимости нашего блога (В это ручками лучше не лезть)
* public - Папка, куда собирается наше приложение (Это то же лучше не трогать, пусть за нас эту папку трогают скрипты gatsby)
* content - В этой папке лежит самое важное - наши статьи и контент для них!
* src - Тут то же очень важная часть нашего блога - исходники блога на React
* static - Папка со статическим контентом, типа фавиконок и т.д.
* gatsby-browser.js - Файл для реализации особенностей в клиентском рендере https://www.gatsbyjs.org/docs/browser-apis/
* gatsby-config.js - Конфиг gatsby https://www.gatsbyjs.org/docs/gatsby-config/
* gatsby-node.js - Файл для реализации особенностей ssr и генерации статического контента https://www.gatsbyjs.org/docs/node-apis/

Ну а остальные файлы нам пока трогать не нужно. (Скорее всего Вы лучше меня знаете, что к чему😜)

И так, наша цель - ```gatsby-config.js```. Откроем его и посмотрим на секцию - siteMetadata. В ней мы можем задавать переменные, которые потом можем использовать внутри React приложения. И так, поменяем на нужные нам:

```javascript
module.exports = {
  siteMetadata: {
    title: `Блог дарта Вейдера`,
    author: {
      name: `Дарт Вейдер`,
      summary: `который знает всё о силе.`,
    },
    description: `Блог самого могучего ситха.`,
    siteUrl: `https://blog.dart-weider.com/`,
    social: {
      twitter: `dart-weider`,
    },
  },
  ...
```

## Шаг 6
## Покажем миру своё лицо

Так, а иконка-то у нас осталась какого-то незнакомого мужика. Заменим её на нашу прекрасную физиономию. Что бы узнать от куда эта иконка перейдём в компонент, отвечающий за рендер информации об авторе - это ```src/components/bio.js```.
Видим там следующие строчки в graphql запросе:

```javascript
...
const data = useStaticQuery(graphql`
    query BioQuery {
      avatar: file(absolutePath: { regex: "/profile-pic.jpg/" }) {
          ...
```

Ага, ```profile-pic.jpg```. Но где же у нас лежит жтот файл? Давайте выясним. Плагин отвечающий за работу с файлами назвается ```gatsby-source-filesystem```. Идём в gatsby-config.js и находим настройки этого плагина:

```javascript
...
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/content/assets`,
        name: `assets`,
      },
    },
...
```

Ага! Вот ты где! В content/assets. Заходим туда и меняем фотку на свою.

## Шаг 7
## Меняем шаблон представления автора

Может случиться так, что по факту, Вам не подходит тот шаблон представления автора, который идёт в стандартной поставке (Например, Вы создаете блог на любом языке, отличном от английского). Тогда нужно исправить эту несправедливость!

Опять таки идем в ```src/components/bio.js```. Находим jsx код и правим на то что нужно нам нужно, например:

```javascript
...
    <p>
        <strong>{author.name}</strong> - {author.summary}
        {` `}
        <a href={`https://twitter.com/${social.twitter}`}>
            You should follow him on Twitter
        </a>
    </p>
...
```

Тут же можно исправить и вывод соц. сетей. Например, если у Вас их нет:

```javascript
...
    <p>
        <strong>{author.name}</strong> - {author.summary}.
        {social?.twitter ? (
          <>
            {` `}
            <a href={`https://twitter.com/${social.twitter}`}>
              Заходите в мой твиттер🤪
            </a>
          </>
        ) : null}
    </p>
...
```

Сделаем возможность не иметь twitter аккаунта и в тегах seo. Это файл ```src/components/seo.jsx```:

```javascript
...
const metaTags = [
    {
      name: `description`,
      content: metaDescription,
    },
    {
      property: `og:title`,
      content: title,
    },
    {
      property: `og:description`,
      content: metaDescription,
    },
    {
      property: `og:type`,
      content: `website`,
    },
    {
      name: `twitter:card`,
      content: `summary`,
    },
    site.siteMetadata?.social?.twitter
      ? {
          name: `twitter:creator`,
          content: site.siteMetadata.social.twitter,
        }
      : undefined,
    {
      name: `twitter:title`,
      content: title,
    },
    {
      name: `twitter:description`,
      content: metaDescription,
    },
  ]

  return (
    <Helmet
      htmlAttributes={{
        lang,
      }}
      title={title}
      titleTemplate={`%s | ${site.siteMetadata.title}`}
      meta={metaTags.filter(tag => tag).concat(meta)}
    />
  )
...
```

## Шаг 8 
## Пишем статью!

Ну, тут всё просто - переходим в папку ```content/blog```. Тут уже созданны пробные статьи. Смотрим что внутри, и делаем так же. Создаем директорию ```create-blog-with-gatsby```. Ложим внутрь файл ```index.md```. Заголовок у markdown файла должен быть такой:

```markdown
---
title: Как создать простой блог с Gatsby
date: "2020-06-06T22:12:03.284Z"
description: "Создаём простой блог с Gatsby"
---
```

И дальше просто пишем, нашу статью используя markdown. Вот и всё!)))

Ну конечно же нет...
Мы дальше можем дорабатывать наш блог, устанавливать плагины статистики, настраивать изображения, использовать локализацию (К примеру, что бы хотя бы даты были на нужном языке) и т.д.
