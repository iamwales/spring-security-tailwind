# Spring Boot + Thymeleaf + Tailwind CSS + Spring Security

This is an example of how to use Tailwind CSS in Spring Boot + Thymeleaf project. It also overs Spring Security for authenticating a simple user.

# Tailwind

You can just use the CDN if you're prototyping something out but if this is a real world project you don't want to do that. When you include the CDN you get all the Tailwind CSS utility classes even if you're not using them.

```html
 <script src="https://cdn.tailwindcss.com"></script>
```
This project only takes the styles that are being used from tailwind and not the entire Tailwind CSS utility classes.

# Frontend

To run the frontend go into `/src/main/frontend` using the command line. Run the following command to install the packages in
`package.json`.

```shell
npm install 
```

We have a script that will take the styles file as input and send the build output to `/resources/static/main.css`:

```json
{
  "name": "frontend",
  "version": "1.0.0",
  "description": "Spring Boot + Tailwind CSS Custom Login Form + Spring Security",
  "scripts": {
    "build": "tailwindcss -i ./styles.css -o ../resources/static/main.css",
    "watch": "tailwindcss --watch -i ./styles.css -o ../resources/static/main.css"
  },
  "author": "Olawale Adeogun",
  "license": "ISC",
  "private": true,
  "devDependencies": {
    "tailwindcss": "^3.4.4"
  },
  "dependencies": {
    "@tailwindcss/forms": "^0.5.7"
  }
}
```

You will need to run `npm run build` or `npm run watch` and then start the Spring Boot application. After running the npm command you should see a new file `main.css` in the `/src/main/resources/static` directory. This contains all the Tailwind CSS utility classes you are using instead of every single one the framework declares resulting in a much smaller size in production.

## Maven Configuration

What we did in the previous section is great for development but what happens when we want to build an artifact for another environment like production. In that scenario we need to automate this process so that the `npm run build` command is run and the output of the build creates a new `main.css` in the static folder in the event this wasn't done locally.

For that we will be using the [frontend-maven-plugin](https://github.com/eirslett/frontend-maven-plugin). This plugin will download/install node and npm, run npm install or any other commands we need it to. First you will need to declare the versions of node and npm you wish to use.

```xml
<properties>
    <java.version>22</java.version>
    <node.version>v20.2.0</node.version>
    <npm.version>6.14.3</npm.version>
</properties>
```

Next setup the plugin to install node and npm, run `npm install` and `npm run build`.

```xml
<plugin>
  <groupId>com.github.eirslett</groupId>
  <artifactId>frontend-maven-plugin</artifactId>
  <version>1.15.0</version>
  <executions>

      <execution>
          <id>install node and npm</id>
          <goals>
              <goal>install-node-and-npm</goal>
          </goals>
          <phase>generate-resources</phase>
          <configuration>
              <nodeVersion>${node.version}</nodeVersion>
              <npmVersion>${npm.version}</npmVersion>
          </configuration>
      </execution>

      <execution>
          <id>npm install</id>
          <goals>
              <goal>npm</goal>
          </goals>
          <phase>generate-resources</phase>
          <configuration>
              <arguments>install</arguments>
          </configuration>
      </execution>

      <execution>
          <id>npm build</id>
          <goals>
              <goal>npm</goal>
          </goals>
          <phase>generate-resources</phase>
          <configuration>
              <arguments>run build</arguments>
          </configuration>
      </execution>

  </executions>
  <configuration>
      <nodeVersion>${node.version}</nodeVersion>
      <workingDirectory>src/main/frontend</workingDirectory>
      <installDirectory>target</installDirectory>
  </configuration>
</plugin>
```

## Resources

- [Tailwind CSS Docs](https://tailwindcss.com/docs)
- [Spring Security Docs](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/form.html)