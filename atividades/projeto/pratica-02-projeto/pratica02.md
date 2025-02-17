# Introdução ao Desenvolvimento Web

## Roteiro de Prática

> **_Prática 02 - Projeto Final_**

### 01 - Refatorando o projeto

- Nesse ponto, nós vamos realizar uma refatoração do projeto.

- _A refatoração é uma forma disciplinada de reestruturar o código quando pequenas mudanças são feitas nele para melhorar o design ou alguma funcionalidade/lógica._ 

- _Um aspecto importante de uma refatoração é que ela melhora o código sem mudar o comportamento do sistema; uma refatoração não adiciona nem remove funcionalidade._

- Vamos começar reorganizando a página inicial da nossa aplicação.

### 02 - Criando um Hook personalizado para fazer consultas à API do TMDB

1. Crie uma pasta chamada `hooks` dentro da pasta `src`

1. Crie um arquivo chamado `useFetch.js`

    - Quando estamos criando um hook próprio (relembre o que é hook em react, [clicando aqui](https://www.alura.com.br/artigos/react-hooks)), geralmente usamos um padrão de nomeação onde começamos o nome do hook com o termo `use`.

    - O nosso hook `useFetch` será o responsável por realizar as chamadas à API do TMDB para buscar dados (daí o nome `fetch`).

    - Dessa forma, não precisaremos repetir código em todo onde seja necessário buscar dados da API, simplesmente chamamos o nosso hook e ele cuida disso.

1. O código que deve ser implementado no arquivo `useFetch.js` é o seguinte:

```javascript
import { useEffect, useState } from "react";

const options = {
  method: "GET",
  headers: {
    accept: "application/json",
    Authorization: `Bearer SEU_TOKEN`,
  },
};

export const useFetch = (url) => {
  const [dados, setDados] = useState(null);

  useEffect(() => {
    const buscarDados = async () => {

      try {
        
        const resposta = await fetch(url, options).then((resposta) => resposta.json());
        setDados(resposta);

      } catch (error) {
        console.log("Erro ao buscar dados: ", error);
      }

    };

    buscarDados();
  }, [url]);

  return { dados };
};
```
    
>❗ Lembre-se de substituir o termo `SEU_TOKEN` pelo seu token exclusivo da API do TMDB.

1. Basicamente, esse arquivo retira do nosso componente `App` (componente inicial) a lógica de fazer a busca de dados na API.
    - Abra o arquivo `App.jsx` e **remova** os seguintes trechos (que já estão em nosso hook):
    ```javascript
    const options = {
        method: 'GET',
        headers: {
            accept: 'application/json',
            Authorization: `Bearer SEU_TOKEN`
        }
    };
    ```
    ```javascript
    const [filmes, setFilmes] = useState([]);

    useEffect(() => {
        const buscarFilmes = async () => {

            try {
                
                const dados = await fetch(URL_FETCH, options).then((resposta) => resposta.json());
                setFilmes(dados.results);

            } catch (error) {
                console.log("Erro ao buscar filmes: ", error);
            }

        };

        buscarFilmes();
    }, []);
    ```

1. Ainda no arquivo `App.jsx`, vamos importar o nosso hook `useFetch` para buscar os filmes na API.
    - Inclua a seguinte linha de importação no início do arquivo `App.jsx`:
    ```javascript
    import { useFetch } from "./hooks/useFetch";
    ```

    - Imediatamente após a linha `const App = () => {`, ou seja, no início do componente App, inclua a seguinte linha de código:
    ```javascript
    const { dados: filmes } = useFetch(URL_FETCH);
    ```
    - O código acima chama o nosso hook `useFetch` passando a URL correta para buscar os dados que vão ser necessários para a página inicial. Além disso, sabendo que o nosso hook retorna um objeto chamado `dados`, precisamos renomear para `filmes` (porque é com esse nome que estamos acessando as propriedades dos filmes e exibindo nos cards).

1. Uma última alteração que precisamos fazer no arquivo `App.jsx` é a seguinte:
    - O trecho:
    ```javascript
    {filmes.map((filme) => (
        <Card key={filme.id} filme={filme} />
    ))}
    ```
    - Deve ser substituído por:
    ```javascript
    {filmes && filmes.results.map((filme) => (
        <Card key={filme.id} filme={filme} />
    ))}
    ```
    - Por quê isso foi necessário?
        - Como estamos usando um hook e ele está fazendo uma chama a uma API, podemos não ter nenhum retorno ou um erro na chamada. 
        - Logo, precisamos ter certeza de que vieram dados da API. A expressão `filmes &&`, faz um teste para saber se a variável `filmes` contém dados e (`&&`) só então prossegue com o código.
        - A expressão `filmes.results` também mudou, porque antes, nosso código já retornava direto os resultados da busca. Agora, nosso hook retorna os dados gerais enviados pela API (pois ele será utilizados para outros dados em outros locais). Então, para dizer que queremos a lista de resultados dessa chamada (a lista de filmes), usamos `filmes.results`.

1. O código final do componente `App.jsx` deve ser o seguinte (confira):
    ```javascript
    import { useFetch } from "./hooks/useFetch";

    import './App.css';

    import NavBar from './components/NavBar';
    import Footer from './components/Footer';
    import Card from './components/Card';

    const URL_FETCH = "https://api.themoviedb.org/3/discover/movie?include_adult=false&include_video=false&language=pt-BR&page=1&sort_by=popularity.desc";

    const App = () => {

    const { dados: filmes } = useFetch(URL_FETCH);

    return (
        <>
            <NavBar />

            <main>
                <div className="container">

                <h2 className="title">Filmes Populares:</h2>

                <div className="movies-container">
                    {filmes && filmes.results.map((filme) => (
                        <Card key={filme.id} filme={filme} />
                    ))}
                </div>

                </div>
            </main>

            <Footer />
        </>
    );
    };

    export default App;
    ```

    - Agora, start o projeto para verificar se o comportamente se mantém o mesmo (exibir os dados dos filmes nos cards).
        - Abra um terminal integrado na pasta do projeto e digite o comando `npm run dev`, seguindo o link exibido (CTRL + CLICK).
