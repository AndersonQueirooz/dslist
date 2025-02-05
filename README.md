# Projeto DSList - Intensivão Java Spring

[![NPM](https://img.shields.io/npm/l/react)](https://github.com/AndersonQueirooz/dslist/blob/main/LICENSE)

## Modelo de domínio DSList

![Modelo de domínio DSList](https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/dslist-model.png)

## Trechos de código

### Plug-in Maven

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<version>3.1.0</version> <!--$NO-MVN-MAN-VER$ -->
</plugin>
```

### application.properties

```
spring.profiles.active=${APP_PROFILE:test}
spring.jpa.open-in-view=false

cors.origins=${CORS_ORIGINS:http://localhost:5173,http://localhost:3000}
```

### application-test.properties

```
# H2 Connection
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# H2 Client
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Show SQL
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

### application-dev.properties

```
#spring.jpa.properties.jakarta.persistence.schema-generation.create-source=metadata
#spring.jpa.properties.jakarta.persistence.schema-generation.scripts.action=create
#spring.jpa.properties.jakarta.persistence.schema-generation.scripts.create-target=create.sql
#spring.jpa.properties.hibernate.hbm2ddl.delimiter=;


spring.datasource.url=jdbc:postgresql://localhost:5433/dscatalog
spring.datasource.username=postgres
spring.datasource.password=1234567

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

### application-prod.properties
```
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

### system.properties
```
java.runtime.version=21
```

### WebConfig

```java
@Configuration
public class WebConfig {

	@Value("${cors.origins}")
	private String corsOrigins;
	
	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/**").allowedMethods("*").allowedOrigins(corsOrigins);
			}
		};
	}
	
}
```

### GameRepository

```java
@Query(nativeQuery = true, value = """
		SELECT tb_game.id, tb_game.title, tb_game.game_year AS `year`, tb_game.img_url AS imgUrl,
		tb_game.short_description AS shortDescription, tb_belonging.position
		FROM tb_game
		INNER JOIN tb_belonging ON tb_game.id = tb_belonging.game_id
		WHERE tb_belonging.list_id = :listId
		ORDER BY tb_belonging.position
			""")
List<GameMinProjection> searchByList(Long listId);

```

### GameListRepository

```java
@Modifying
@Query(nativeQuery = true, value = "UPDATE tb_belonging SET position = :newPosition WHERE list_id = :listId AND game_id = :gameId")
void updateBelongingPosition(Long listId, Long gameId, Integer newPosition);
```

### import.sql
```sql
INSERT INTO tb_game_list (name) VALUES ('Aventura e RPG');
INSERT INTO tb_game_list (name) VALUES ('Jogos de plataforma');

INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Mass Effect Trilogy', 4.8, 2012, 'Role-playing (RPG), Shooter', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/1.png', 'O jogo se passa no ano de 2183. O jogador assume o controle de um soldado de elite chamado Shepard, que é mandado em missões de exploração pela galáxia em uma nave espacial, chamada Normandy. O título Mass Effect faz referência a uma tecnologia presente no jogo, que permite a viagem em uma velocidade mais rápida que a luz.', 'Na pele do Comandante Shepard, chega ao estatuto do melhor soldado de elite da galáxia e lidera uma guerra aberta para deteres um antigo e implacável inimigo: os Reapers.');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Red Dead Redemption 2', 4.7, 2018, 'Role-playing (RPG), Adventure', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/2.png', 'A jogabilidade é apresentada tanto em uma perspectiva em primeira quanto em terceira pessoa, com o jogador sendo livre para explorar e interagir com o mundo aberto.', 'Estados Unidos, 1899. O fim da era do velho oeste se aproxima, e os xerifes caçam as últimas gangues fora da lei. Quem não se rende ou sucumbe, acaba morto. Depois de tudo dar errado em um roubo na cidade de Blackwater, no faroeste, Arthur Morgan e a gangue Van der Linde são obrigados a fugir..');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('The Witcher 3: Wild Hunt', 4.7, 2014, 'Role-playing (RPG), Adventure', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/3.png', 'Ambientado em um gigantesco cenário medieval que dá liberdade total ao jogador, o game, que possui uma jogabilidade não linear e é jogado através de uma perspectiva em terceira pessoa. ' 'Você é Geralt de Rívia, mercenário matador de monstros. Você está em um continente devastado pela guerra e infestado de monstros para você explorar à vontade. Sua tarefa é encontrar Ciri, a Criança da Profecia — uma arma viva que pode alterar a forma do mundo.');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Sekiro: Shadows Die Twice', 3.8, 2019, 'Role-playing (RPG), Adventure', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/4.png', 'Shadows Die Twice é um jogo de ação e aventura intenso em terceira pessoa que se passa no Japão do século XVI. Entre na pele de um guerreiro que caiu em desgraça e voltou da morte, cuja missão é salvar seu mestre e se vingar do seu arqui-inimigo.', 'A jogabilidade é focada em furtividade, exploração e combate, com ênfase especial em batalhas contra chefes. Embora a maior parte do jogo ocorra em áreas fictícias, algumas áreas são fortemente inspiradas em edifícios e locais do mundo real no Japão. O jogo também faz fortes referências à mitologia e filosofia budista. Enquanto criava o jogo, o diretor Hidetaka Miyazaki queria criar uma nova propriedade intelectual (IP) que marcasse um ponto de partida da série de jogos Souls, também feita pela FromSoftware, e buscou inspiração em séries como Tenchu.');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Ghost of Tsushima', 4.6, 2012, 'Role-playing (RPG), Adventure', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/5.png', 'A história acompanha Jin Sakai, um samurai que precisa proteger a Ilha de Tsushima durante a primeira invasão mongol do Japão.', 'Ghost of Tsushima é um jogo eletrônico de ação-aventura desenvolvido pela Sucker Punch Productions e publicado pela Sony Interactive Entertainment. A história acompanha Jin Sakai, um samurai que precisa proteger a Ilha de Tsushima durante a primeira invasão mongol do Japão. Ele se vê dividido entre escolher seguir o código do guerreiro para lutar honradamente ou usar métodos práticos e desonrosos para subjugar os mongóis.');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Super Mario World', 4.7, 1990, 'Platform', 'Super Ness, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/6.png', 'O jogador navega por meio de duas telas: um mapa do overworld e um percurso de rolagem lateral. O overworld exibe uma representação aérea do mundo atual e possui vários caminhos que levam da entrada do mundo a um castelo.', 'Super Mario World é um jogo eletrônico de plataforma 2D de rolagem lateral, no qual o jogador controla os protagonistas Mario ou Luigi. Sua jogabilidade é semelhante à dos títulos anteriores da série Super Mario – Super Mario Bros., Super Mario Bros. 2 e Super Mario Bros. 3 – mas introduz novos elementos. Além de correr e pular, o jogador também pode voar com o auxílio de itens especiais e executar novos tipos de saltos, como o salto giratório.');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Hollow Knight', 4.6, 2017, 'Platform', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/7.png', 'Hollow Knight é um jogo de ação e aventura em estilo metroidvania 2D que se passa em Hallownest, antigo reino fictício.', 'No jogo, um cavaleiro sem nome explora um reino em ruínas habitado por insetos, desbloqueando habilidades novas que ajudam a explorar e livrar o reino de uma infecção causada por um deus esquecido.');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Ori and the Blind Forest', 4, 2015, 'Platform', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/8.png', 'Conta a história de um jovem órfão destinado ao heroísmo, em um jogo de Ação em Plataformas com visual incrível criado pela Moon Studios.', 'O jogo, o jogador assume o controle de Ori, um espírito branco guardião, e Sein, a "luz e olhos" do Espírito da Árvore na Floresta. Para progredir na história, os jogadores devem mover entre plataformas e solucionar quebra-cabeças. O jogo apresenta um sistema chamada "ligações da alma", que permite ao jogador salvar seu progresso quando desejar, e um sistema de melhoras que dá aos jogadores a habilidade de evoluir as técnicas e poderes de Ori.');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Cuphead', 4.6, 2017, 'Platform', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/9.png', 'Cuphead apresenta dois jogadores que controlam o personagem-título e seu irmão Mugman, em uma aventura através da fictícia Inkwell Isle para derrotar uma série de chefes para pagar uma dívida adquirida com o diabo.', 'Cuphead é um clássico jogo de ação de corridas e armas altamente focalizado em batalhas de chefes. Foi inspirado nos desenhos da década de 1930; os visuais e o áudio foram minuciosamente criados com as mesmas técnicas da era, ou seja, aquarelas e animações tradicionais desenhadas à mão e gravações de jazz originais.');
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Sonic CD', 4, 1993, 'Platform', 'Sega CD, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/10.png', 'A história se passa no Little Planet, um pequeno mundo que surge em Never Lakes durante um mês do ano. Nele, o passado, o presente e o futuro se unem formando as Time Stones, pedras que dão o poder de viajar no tempo.', 'A história se passa no Little Planet, um pequeno mundo que surge em Never Lakes durante um mês do ano. Nele, o passado, o presente e o futuro se unem formando as Time Stones, pedras que dão o poder de viajar no tempo. Eggman descobre a existência delas e decide encontrá-las antes de Sonic. Para impedir o ouriço, Eggman cria Metal Sonic, réplica metálica do Sonic, que captura Amy Rose. Cabe a Sonic salvá-la, destruir Metal Sonic e acabar com os planos de Eggman.');

INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 1, 0);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 2, 1);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 3, 2);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 4, 3);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 5, 4);

INSERT INTO tb_belonging (list_id, game_id, position) VALUES (2, 6, 0);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (2, 7, 1);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (2, 8, 2);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (2, 9, 3);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (2, 10, 4);

```

### Script Docker Compose

https://gist.github.com/acenelio/5e40b27cfc40151e36beec1e27c4ff71

