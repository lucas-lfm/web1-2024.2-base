# **Prática 04 - Página de Detalhes do Filme**

## **Objetivos**
- Implementar uma página de detalhes do filme, que exibe um **banner**, informações básicas do filme (título, gêneros, nota, etc.), **trailer** e **sinopse**.
- Integrar a navegação para que os filmes listados nas páginas **Home** e **Search** redirecionem para essa nova página.
- O resultado final esperado pode ser visualizado abaixo:

![Demosntração do resultado final esperado](./img-instrucoes/demo-cinelib-v4.gif)

- Veja também o vídeo demonstrativo [neste link](https://youtu.be/NtMivMSq_0A).

---

## **1. Criando a Página de Detalhes e Configurando sua Rota**

1. Crie uma nova pasta, chamada `Filme`, dentro da pastas de páginas (`pages`).

1. Dentro da pasta `Filme`, crie um arquivo de componente, chamado `Filme.jsx`, e insira um código base, como a seguir:

    ```javascript
    const Filme = () => {

        return (
            <div id='filme'>
                <h2>Página de detalhes do filme.</h2>
            </div>
        );
    };

    export default Filme;
    ```

1. Abra o arquivo **`main.jsx`** e **adicione uma nova rota** dentro do array `children`, além de já importar o componente correspondente à página de detalhes, que você criou anteriormente. O arquivo `main.jsx` atualizado, ficará assim:
    ```javascript
    import { StrictMode } from 'react';
    import { createRoot } from 'react-dom/client';
    import { createBrowserRouter, RouterProvider } from 'react-router-dom';

    // pages
    import App from './App.jsx';
    import Home from './pages/Home/Home.jsx';
    import Filme from './pages/Filme/Filme.jsx';
    import Search from './pages/Search/Search.jsx';

    import './index.css';

    const router = createBrowserRouter([
        {
            path: "/",
            element: <App />,
            children: [
                {
                    path: "/",
                    element: <Home />,
                },
                {
                    path: "/search",
                    element: <Search />,
                },
                {
                    path: "/filme/:id",
                    element: <Filme />,
                }
            ]
        }
    ]);

    createRoot(document.getElementById('root')).render(
        <StrictMode>
            <RouterProvider router={router} />
        </StrictMode>
    )
    ```
    - ⚠️ A rota `/filme/:id` indica que **":id"** será um parâmetro dinâmico, representando o ID do filme que o usuário deseja visualizar.

---

## **2. Ajustando os Links dos Filmes**
Agora, precisamos modificar os **cards dos filmes** para que, ao serem clicados, levem para a página de detalhes.

1. Abra **`Card.jsx`** e modifique o botão "Detalhes" para ser um link usando `Link` do `react-router-dom`:
    ```javascript
    import "./Card.css";
    import starIcon from "./../assets/star-icon.svg";
    import { Link } from "react-router-dom";

    const Card = ( { filme } ) => {
        return (
            <div className="movie-card">
                <img src={`https://image.tmdb.org/t/p/w500${filme.poster_path}`} alt={`Imagem de divulgação do filme ${filme.title}`} />
                <h2>{filme.title}</h2>
                <p>
                    <img src={starIcon} alt="Ícone de estrela para indicar nota de avaliação" /> {filme.vote_average.toFixed(2)}
                </p>
                <Link to={`/filme/${filme.id}`}>Detalhes</Link>
            </div>
        );
    };

    export default Card;
    ```
    - ⚠️ A linha `<Link to={`/filme/${filme.id}`}>Detalhes</Link>` faz com que cada card direcione o usuário para a página de detalhes do respectivo filme, passando seu identificador (`id`).

1. Inicie a aplicação com `npm run dev` e teste a navegação clicando no botão `Detalhes` de um card de filme. Você deve ser redirecionado para a página de filmes e visualizar o texo que definimos nela.

---

## **3. Criando o Componente do Banner**
O banner exibe uma **imagem de fundo grande** e o título do filme.

1. Crie os arquivos `Banner.jsx` e `Banner.css` dentro da pasta `components`.
2. O código do componente Banner (arquivo `Banner.jsx`) é o seguinte:
    ```javascript
    import "./Banner.css";

    const URL_IMG_BASE = "https://image.tmdb.org/t/p/original";

    const Banner = ({ filme }) => {
      return (
        <div className="banner">
          <img className="banner-img" src={`${URL_IMG_BASE}${filme.backdrop_path}`} alt='' />
          <div className='banner-title'>
            <h1>{`${filme.title} (${new Date(filme.release_date).getFullYear()})`}</h1>
          </div>
        </div>
      );
    };

    export default Banner;
    ```
    - O TMDB disponibiliza as imagens através a URL base `https://image.tmdb.org/t/p/original`. Logo, precisamos concatenar a identificação da imagem que qeremos exibir, no caso a imagem de backdrop (própria para exibir no plano de fundo de um elemento banner, com a URL base das imagens).

3. O seguinte **CSS** (`Banner.css`) deve ser usado para estilizar o componente Banner:
    ```css
    .banner {
        height: 500px;
        overflow: hidden;
        position: relative;
    }

    .banner-img {
        width: 100%;
        max-height: 100%;
        object-fit: cover;
        object-position: top;
    }

    .banner-title {
        position: absolute;
        bottom: 0;
        padding: 0.5rem 1rem;
        width: 100%;
        background-image: linear-gradient(
            to bottom,
            rgba(0, 0, 0, 0.1),
            rgba(0, 0, 0, 0.6)
        );
    }

    .banner-title h1 {
        font-size: 1.5rem;
    }
    ```
    - De forma básica, esse CSS ajusta o tamanho da imagem para ocupar toda a área do banner e cria um gradiente de cores por trás do título do filme, fazendo o texto ficar visível e legível, mesmo quando a imagem tem cores que não ofereçam um bom contraste com o texto.

---

## **4. Criando o Componente de Detalhes**
O componente **Details** exibe informações sobre o filme, incluindo **poster, gêneros, nota, trailer e sinopse**.

1. Crie os arquivos `Details.jsx` e `Details.css` na pasta `components`.

1. Já adicione o CSS necessário no arquivo `Details.css`:
    ```css
    .details {
        padding: 1rem;
        width: 70%;
        min-width: 720px;
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        column-gap: 2%;
        row-gap: 2rem;
        margin: 0 auto;
    }

    .basic-info {
        flex: 1 1 28%;
    }

    .basic-info .img-poster {
        width: 200px;
        border-radius: 4px;
    }

    .basic-info h3 {
        font-size: 1rem;
        margin-top: 1rem;
        margin-bottom: 0.5rem;
    }

    .basic-info p {
        font-size: 0.8rem;
    }

    .basic-info ul {
        list-style-type: none;
        margin-bottom: 1rem;
        font-size: 0.8rem;
        font-style: italic;
        opacity: 0.5;
    }

    .more-info {
        flex: 1 1 70%;
        line-height: 1.5;
        text-align: justify;
    }

    .more-info h3 {
        margin-bottom: 1rem;
    }

    .more-info iframe {
        width: 100%;
        margin-bottom: 1.5rem;
    }
    ```

1. O código inteiro do componente `Details.jsx` é o seguinte:

    ```javascript
    import "./Details.css";

    import starIcon from "./../assets/star-icon.svg";
    import { useFetch } from "../hooks/useFetch";
    import { useEffect, useState } from "react";

    const URL_IMG_BASE = "https://image.tmdb.org/t/p/original";
    const URL_FETCH = "https://api.themoviedb.org/3/movie/";

    const Details = ({ filme }) => {
        const { dados: videos } = useFetch(`${URL_FETCH}${filme.id}/videos?language=pt-BR`);
        const [trailer, setTrailer] = useState(null);

        useEffect(() => {
            if (videos) {
                const trailers = videos.results.filter((video) => video.type === "Trailer");

                if (trailers.length > 0) {
                    setTrailer(trailers[0].key);
                }
            }
        }, [videos]);

        return (
            <div className='details'>
                <div className='basic-info'>
                    <img className='img-poster' src={`${URL_IMG_BASE}${filme.poster_path}`} alt='' />

                    <h3>Gêneros:</h3>
                    <ul className='generos'>
                        {filme.genres.map((genero) => (
                            <li key={genero.id}> {genero.name} </li>
                        ))}
                    </ul>

                    <h3>Avaliação Média: </h3>
                    <p>
                        <img src={starIcon} alt='Ícone de estrela para indicar nota de avaliação' />{" "}
                        <span>
                            {filme.vote_average.toFixed(2)} ({filme.vote_count})
                        </span>
                    </p>
                </div>

                <div className='more-info'>
                    {trailer && (
                        <iframe
                            width='560'
                            height='315'
                            src={`https://www.youtube.com/embed/${trailer}`}
                            title='YouTube video player'
                            allow='accelerometer; autoplay; clipboard-write; 
                            encrypted-media; gyroscope; picture-in-picture; web-share'
                            allowFullScreen>
                        </iframe>
                    )}

                    <h3>Resumo:</h3>
                    <p>{filme.overview}</p>
                </div>
            </div>
        );
    };

    export default Details;
    ```
    > ❗ Como o código desse componente é muito extenso, os próximos passos explicam ele em detalhes e passo a passo.

1. No arquivo `Details.jsx` fizemos as importações necessárias. Nesse componente, precisamos importar, além do CSS:
    - o ícone de estrela (para representar a avaliação);
    - o nosso *hook* `useFetch` para buscar os dados do filme e o trailer; e
    - os *hooks* `useState` e `useEffect` para gerenciar os dados como estado do nosso componente e disparar o evento de buscar/atualizar os dados.

    ```javascript
    import "./Details.css";

    import starIcon from "./../assets/star-icon.svg";
    import { useFetch } from "../hooks/useFetch";
    import { useEffect, useState } from "react";
    ```

1. Depois, definimos as URLs base para a imagem de divulgação e para o *endpoint* que devolve os dados de um filme específico.

    ```javascript
    const URL_IMG_BASE = "https://image.tmdb.org/t/p/original";
    const URL_FETCH = "https://api.themoviedb.org/3/movie/";
    ```

1. Dentro da lógica do componente, inicialmente usamos o `useFetch` para buscar a lista de vídeos disponíveis para o filme em específico, que foi recebido por *props* nesse componente. Além disso, criamos um estado chamado `trailer` para armazenar a identificação do trailer do filme.
    ```javascript
    const { dados: videos } = useFetch(`${URL_FETCH}${filme.id}/videos?language=pt-BR`);
    const [trailer, setTrailer] = useState(null);
    ```
    - Perceba que montamos a URL de consulta à API concatenando a URL base, o ID do filme e o restante do caminho definido para esse *endpoint* na API. Isso está na [documentação](https://developer.themoviedb.org/reference/intro/getting-started) da API do TMDB.

1. No `useEffect`, colocamos a lógica necessária para buscar o primeiro trailer do filme sempre que as informações sobre os vídeos do filme forem atualizadas.
    ```javascript
    useEffect(() => {
        if (videos) {
        const trailers = videos.results.filter((video) => video.type === "Trailer");

        if (trailers.length > 0) {
            setTrailer(trailers[0].key);
        }
        }
    }, [videos]);
    ```
    - Aqui, note que se houver vídeos disponíveis para o filme, nós filtramos somente os vídeos do tipo "Trailer".
    - Depois, verificamos se há trailers disponíveis (`trailers.length > 0`). Se houver, pegamos o atributo `key` e armazenamos no estado `trailer` de nossa aplicação.
    - Esse atributo `key`, é justamente o identificador do trailer no YouTube.

1. Por fim, destaco o conteúdo interno da div com classe `more-info`:
    ```html
    <div className='more-info'>
        {trailer && (
          <iframe
            width='560'
            height='315'
            src={`https://www.youtube.com/embed/${trailer}`}
            title='YouTube video player'
            allow='accelerometer; autoplay; clipboard-write; 
    encrypted-media; gyroscope; picture-in-picture; web-share'
            allowFullScreen
          ></iframe>
        )}

        <h3>Resumo:</h3>
        <p>{filme.overview}</p>
    </div>
    ```
    - Essa parte do componente é justamente onde será exibido o trailer e o resumo do filme.
    - Aqui, estamos usando um elemento `iframe` no modelo disponibilizado pelo próprio YouTube para incorporar vídeos em nossa página.
    - Veja que ao final da URL do vídeo (atributo `src`), passamos nosso estado `trailer` que guarda o identificador do vídeo.
    - O detalhe aqui é que a área de vídeo só será adicionada se houver trailer. Se o estado `trailer` não existir, a verificação `trailer &&` retornará falso e o código seguinte (contendo o `iframe`) não será executado.
    - Isso evita erros ao tentar adicionar uma área de player de vídeo para um recurso que não existe.

---

## **5. Adicionando os Componentes da Página de Detalhes do Filme**

1. Volte ao arquivo **`Filme.jsx`** e atualize-o para adicionar os componentes `Banner` e `Details` e para adicionar a lógica necessária para buscar os dados do filme:
    ```javascript
    import { useParams } from "react-router-dom";
    import { useFetch } from "../../hooks/useFetch";
    import Banner from "../../components/Banner";
    import Details from "../../components/Details";

    const URL_FETCH = "https://api.themoviedb.org/3/movie/";

    const Filme = () => {
        const { id } = useParams();
        const { dados: filme } = useFetch(`${URL_FETCH}${id}?language=pt-BR`);

        return (
            <div id='filme'>
                {filme && (
                    <>
                        <Banner filme={filme} />
                        <Details filme={filme} />
                    </>
                )}
            </div>
        );
    };

    export default Filme;
    ```
    - O **`useParams()`** é utilizado para obter o ID do filme na URL passada na navegação.
    - O hook **`useFetch()`** busca os dados do filme com base nesse ID.
    - **Banner** e **Details** são componentes reutilizáveis para organizar a exibição das informações.

---

## **6. Ajustes Finais e Testes**

Agora que a página está implementada, verifique se tudo está funcionando corretamente:

✔ Digite `npm run dev` no terminal, caso ainda não tenha iniciado a aplicação, e clique no botão `Detalhes` no card de algum filme.  
✔ Verifique se o banner carrega corretamente.  
✔ Confirme se as informações aparecem completas (poster, gêneros, nota, trailer e sinopse).  
✔ Teste diferentes filmes para garantir que os dados carregam corretamente.  

> ❗ Se algum filme não tiver o trailer carregado, possivelmente ele não tem trailer disponível no YouTube.

Caso tudo esteja funcionando, a **Prática 4 está concluída**!