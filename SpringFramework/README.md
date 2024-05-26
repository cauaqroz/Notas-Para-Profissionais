# Capítulo 1: Começando com o Spring Framework
| Versão | Data de Lançamento | Versão | Data de Lançamento | Versão | Data de Lançamento | Versão | Data de Lançamento |
| ------ | ------------------ | ------ | ------------------ | ------ | ------------------ | ------ | ------------------ |
| 5.0.x  | 24-10-2017         | 4.3.x  | 10-06-2016         | 4.2.x  | 31-07-2015         | 4.1.x  | 14-09-2014         |
| 4.0.x  | 12-12-2013         | 3.2.x  | 13-12-2012         | 3.1.x  | 13-12-2011         | 3.0.x  | 17-12-2009         |
| 2.5.x  | 25-12-2007         | 2.0.x  | 04-10-2006         | 1.2.x  | 13-05-2005         | 1.1.x  | 05-09-2004         |
| 1.0.x  | 24-03-2003         |        |                    |        |                    |        |                    |


## Seção 1.1: Configuração (XML)
#### Passos para criar um "Hello Spring":

1. Investigue o **Spring Boot** para ver se ele se adequa melhor às suas necessidades.
2. Tenha um projeto configurado com as **dependências corretas**. É recomendado que você use o **Maven** ou o **Gradle**.
3. Crie uma classe **POJO**, por exemplo, `Employee.java`.
4. Crie um arquivo **XML** onde você possa definir sua classe e variáveis. Por exemplo, `beans.xml`.
5. Crie sua classe principal, por exemplo, `Customer.java`.
6. Inclua o **spring-beans** (e suas dependências transitivas!) como uma dependência.

### Employee.java:

    package com.test;

    public class Employee {
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public void displayName() {
            System.out.println(name);
        }
    }



### beans.xml:

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd">

        <bean id="employee" class="com.test.Employee">
            <property name="name" value="test spring"></property>
        </bean>
    </beans>

### Customer.java:

    package com.test;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class Customer {
        public static void main(String[] args) {
            ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
            Employee obj = (Employee) context.getBean("employee");
            obj.displayName();
        }
    }

## Seção 1.2: Apresentando os Recursos Principais do Spring por Exemplo
Descrição:

Este é um exemplo de execução independente, incluindo/apresentando: dependências mínimas necessárias, Configuração Java, declaração de Bean por anotação e Configuração Java, Injeção de Dependência por Construtor e por Propriedade, e ganchos Pré/Pós.

#### Dependências:

Estas dependências são necessárias no classpath:

1. spring-core
2. spring-context
3. spring-beans
4. spring-aop
5. spring-expression
6. commons-logging

#### Classe Principal:

Começando do fim, esta é a nossa classe Main que serve como um espaço reservado para o método main(), que inicializa o Application Context apontando para a classe de Configuração e carrega todos os vários beans necessários para mostrar a funcionalidade específica.

    package com.stackoverflow.documentation;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.annotation.AnnotationConfigApplicationContext;

    public class Main {
        public static void main(String[] args) {
            // inicializando o Application Context uma vez por aplicação.
            ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);

            // bean registrado por anotação
            BeanDeclaredByAnnotation beanDeclaredByAnnotation = applicationContext.getBean(BeanDeclaredByAnnotation.class);
            beanDeclaredByAnnotation.sayHello();

            // bean registrado por arquivo de configuração Java
            BeanDeclaredInAppConfig beanDeclaredInAppConfig = applicationContext.getBean(BeanDeclaredInAppConfig.class);
            beanDeclaredInAppConfig.sayHello();

            // mostrando injeção de construtor
            BeanConstructorInjection beanConstructorInjection = applicationContext.getBean(BeanConstructorInjection.class);
            beanConstructorInjection.sayHello();

            // mostrando injeção de propriedade
            BeanPropertyInjection beanPropertyInjection = applicationContext.getBean(BeanPropertyInjection.class);
            beanPropertyInjection.sayHello();

            // mostrando ganchos PreConstruct / PostDestroy
            BeanPostConstructPreDestroy beanPostConstructPreDestroy = applicationContext.getBean(BeanPostConstructPreDestroy.class);
            beanPostConstructPreDestroy.sayHello();
        }
    }

#### Arquivo de Configuração da Aplicação:

A classe de configuração é anotada por @Configuration e é usada como um parâmetro no Application Context inicializado. A anotação @ComponentScan no nível da classe de configuração aponta para um pacote a ser escaneado em busca de Beans e dependências registradas usando anotações. Finalmente, a anotação @Bean serve como uma definição de bean na classe de configuração.


    package com.stackoverflow.documentation;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.ComponentScan;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    @ComponentScan("com.stackoverflow.documentation")
    public class AppConfig {
        @Bean
        public BeanDeclaredInAppConfig beanDeclaredInAppConfig() {
            return new BeanDeclaredInAppConfig();
        }
    }

#### Declaração de Bean por Anotação:

A anotação @Component serve para demarcar o POJO como um bean Spring disponível para registro durante a varredura de componentes.

    @Component
    public class BeanDeclaredByAnnotation {
        public void sayHello() {
            System.out.println("Hello, World from BeanDeclaredByAnnotation !");
        }
    }

#### Declaração de Bean por Configuração de Aplicação:

Observe que não precisamos anotar ou marcar nosso POJO, pois a declaração/definição do bean está acontecendo no arquivo da classe de Configuração do Aplicativo.

    public class BeanDeclaredInAppConfig {
        public void sayHello() {
            System.out.println("Hello, World from BeanDeclaredInAppConfig !");
        }
    }

#### Injeção de Construtor:

Observe que a anotação @Autowired é definida no nível do construtor. Observe também que, a menos que explicitamente definido por nome, a autowiring padrão acontece com base no tipo do bean (neste caso, BeanToBeInjected).

    package com.stackoverflow.documentation;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Component;

    @Component
    public class BeanConstructorInjection {
        private BeanToBeInjected dependency;

        @Autowired
        public BeanConstructorInjection(BeanToBeInjected dependency) {
            this.dependency = dependency;
        }

        public void sayHello() {
            System.out.print("Hello, World from BeanConstructorInjection with dependency: ");
            dependency.sayHello();
        }
    }


### Injeção de Propriedade:

Observe que a anotação @Autowired demarca o método setter cujo nome segue o padrão JavaBeans.


    package com.stackoverflow.documentation;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Component;

    @Component
    public class BeanPropertyInjection {
        private BeanToBeInjected dependency;    
    
        @Autowired
        public void setBeanToBeInjected(BeanToBeInjected beanToBeInjected) {
            this.dependency = beanToBeInjected;
    
        }
    
        public void sayHello() {
            System.out.println("Hello, World from BeanPropertyInjection !");
        }
      }
