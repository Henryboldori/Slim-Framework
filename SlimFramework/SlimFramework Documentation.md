Get Started
    Home
        Slim é um microframework PHP que ajuda você a escrever rapidamente aplicativos e APIs da web simples, porém poderosos. No seu núcleo, Slim é um despachante que recebe uma solicitação HTTP, invoca uma rotina de retorno de chamada apropriada e retorna uma resposta HTTP
    Installation
    Upgrade Guide
    Web Servers
        É comum usar o padrão Front-Controller para direcionar solicitações HTTP recebidas pelo seu servidor web para um único arquivo PHP. As instruções abaixo explicam como configurar seu servidor web para enviar solicitações HTTP para o arquivo PHP do front-controller.
        O servidor web embutido foi projetado para auxiliar no desenvolvimento de aplicações. Ele pode ser útil para testes ou para demonstrações de aplicações que são executadas em ambientes controlados. Não é destinado a ser um servidor web completo. Não deve ser usado em uma rede pública.
    Deployment
Concepts
    Application Life Cycle
    PSR-7
        Slim suporta interfaces PSR-7 para seus objetos de Requisição (Request) e Resposta (Response). Isso torna o Slim flexível, pois ele pode usar qualquer implementação PSR-7. Por exemplo, você pode retornar uma instância de GuzzleHttp\Psr7\CachingStream ou qualquer instância retornada pela função GuzzleHttp\Psr7\stream_for().
    Middleware
        Middleware pode realizar qualquer operação adequada com esses objetos, mas é obrigatório que um middleware retorne uma instância de Psr\Http\Message\ResponseInterface. Cada middleware deve invocar o próximo middleware, passando o objeto Request como argumento.
        Slim adiciona middleware como camadas concêntricas ao redor da aplicação principal. Cada nova camada de middleware envolve as camadas de middleware existentes. A estrutura concêntrica expande-se para fora à medida que camadas adicionais de middleware são adicionadas.
    Dependency Container
        O Slim utiliza um Dependency Container opcional para preparar, gerenciar e injetar dependências da aplicação. O Slim suporta containers que implementam a PSR-11, como o PHP-DI.
The Application
    Overview
    Notices & Warnings Handling
The Request
    Overview
    Method
        Cada requisição HTTP possui um método que é tipicamente um dos seguintes: GET   POST    PUT  DELETE  HEAD    PATCH   OPTIONS
    Headers
        Toda requisição HTTP possui cabeçalhos. Estes são metadados que descrevem a requisição HTTP, mas não são visíveis no corpo da requisição. O objeto de Requisição PSR-7 do Slim fornece diversos métodos para inspecionar seus cabeçalhos.
    Body
        Toda requisição HTTP possui um corpo. Se você está construindo uma aplicação Slim que consome dados JSON ou XML, você pode usar o método getParsedBody() do objeto de Requisição PSR-7 para analisar o corpo da requisição HTTP em um formato nativo do PHP. Note que a análise do corpo difere de uma implementação PSR-7 para outra.
    Uploaded Files
        Os uploads de arquivos em $_FILES estão disponíveis a partir do método getUploadedFiles() do objeto de Requisição. Isso retorna um array indexado pelo nome do elemento de entrada.
    Helpers
    Route object
The Response
    Overview
        O objeto de resposta PSR-7 é injetado nas rotas da sua aplicação Slim como o segundo argumento para o retorno de chamada da rota
    Status
        Toda resposta HTTP possui um código de status numérico. O código de status identifica o tipo de resposta HTTP a ser retornada ao cliente. O código de status padrão do objeto de Resposta PSR-7 é 200 (OK). Você pode obter o código de status do objeto de Resposta PSR-7 com o método getStatusCode() desta forma.
    Headers
    Body
    JSON
        Os dados JSON podem ser retornados com o código de status HTTP padrão 200.
Routing
    Overview
    Create Routes
        Pode definir rotas de aplicativos usando métodos de proxy na instância Slim\App. O Slim Framework fornece métodos para os métodos HTTP mais populares.
    Callbacks
        Cada método de roteamento descrito acima aceita uma rotina de retorno de chamada como seu argumento final. Este argumento pode ser qualquer chamável PHP e, por padrão, aceita três argumentos.
            Request: O primeiro argumento é um objeto Psr\Http\Message\ServerRequestInterface que representa a requisição HTTP atual.
            Response: O segundo argumento é um objeto Psr\Http\Message\ResponseInterface que representa a resposta HTTP atual.
            Argumentos: O terceiro argumento é um array associativo que contém valores para os espaços reservados nomeados da rota atual.
        Escrevendo conteúdo na resposta
        Existem duas maneiras de escrever conteúdo na resposta HTTP:
            Usando o método $response->getBody()->write('meu conteúdo'); do objeto Response.
            Simplesmente usando echo() para o conteúdo no retorno de chamada da rota. Esse conteúdo será anexado ou prefixado ao objeto de resposta HTTP atual se você adicionar o Middleware de Bufferização de Saída.
    Strategies
        A assinatura do retorno de chamada da rota é determinada por uma estratégia de rota. Por padrão, o Slim espera que os retornos de chamada da rota aceitem a requisição, resposta e um array de argumentos de espaços reservados da rota. Isso é chamado de estratégia RequestResponse. No entanto, pode alterar a assinatura esperada do retorno de chamada da rota simplesmente usando uma estratégia diferente. Como exemplo, o Slim fornece uma estratégia alternativa chamada RequestResponseArgs que aceita requisição e resposta, além de cada espaço reservado da rota como argumento separado.
    Placeholders
        Cada método de roteamento descrito aceita um padrão de URL que é correspondido à URI da requisição HTTP atual. Os padrões de rota podem usar espaços reservados nomeados para corresponder dinamicamente aos segmentos da URI da requisição HTTP.
    Names
        As rotas da aplicação podem ser atribuídas a um nome. Isso é útil se você deseja gerar programaticamente uma URL para uma rota específica com o método urlFor() do RouteParser. Cada método de roteamento descrito acima retorna um objeto Slim\Route, e este objeto expõe um método setName().
    Groups
        Para ajudar a organizar rotas em grupos lógicos, o Slim\App também fornece um método group(). O padrão de rota de cada grupo é prefixado às rotas ou grupos contidos dentro dele
    Middleware
    Caching
    Container Resolution
        No Slim, existem algumas maneiras diferentes de definir as funções de ação das suas rotas.
            Além de uma função, você pode usar:
            container_key:method
            Class:method
            Class implementing __invoke() method
            container_key
            Essa funcionalidade é habilitada pela Classe de Resolução de Chamáveis do Slim. Ela traduz uma entrada de string em uma chamada de função
Packaged Middleware
    Routing
        O roteamento foi implementado como middleware. Usando o FastRoute como roteador padrão, mas não está intimamente acoplado a ele. Se quisesse implementar outra biblioteca de roteamento, poderia criar suas próprias implementações das interfaces de roteamento. DispatcherInterface, RouteCollectorInterface, RouteParserInterface e RouteResolverInterface criam uma ponte entre os componentes do Slim e a biblioteca de roteamento. Se estiver usando determineRouteBeforeAppMiddleware, precisará adicionar o middleware Middleware\RoutingMiddleware à sua aplicação logo antes da chamada para run() para manter o comportamento anterior.
    Error Handling
        Cada aplicação do Slim Framework possui um manipulador de erros que recebe todas as exceções não capturadas do PHP. Este manipulador de erros também recebe os objetos de requisição e resposta HTTP atuais. O manipulador de erros deve preparar e retornar um objeto de Resposta apropriado para ser retornado ao cliente HTTP.
    Method Overriding
        O Middleware de Sobrescrita de Método permite que você use o cabeçalho de requisição X-Http-Method-Override ou o parâmetro do corpo da requisição _METHOD para substituir o método de uma requisição recebida. O middleware deve ser colocado após o middleware de roteamento ter sido adicionado.
    Output Buffering
        O Middleware de Bufferização de Saída permite que você alterne entre dois modos de bufferização de saída: APPEND (padrão) e PREPEND. O modo APPEND usará o corpo de resposta existente para acrescentar o conteúdo. O modo PREPEND criará um novo objeto de corpo de resposta e acrescentará o conteúdo à saída do corpo de resposta existente. Este middleware deve ser colocado no centro da pilha de middlewares para que seja executado por último.
    Body Parsing
        É muito comum em APIs da web enviar dados no formato JSON ou XML. Por padrão, as implementações do PSR-7 não suportam esses formatos, sendo necessário decodificar o método getBody() do objeto de Requisição por conta própria. Como essa é uma necessidade comum, o Slim 4 fornece o BodyParsingMiddleware para lidar com essa tarefa.
    Content Length  
        O  Middleware de Comprimento de Conteúdo irá automaticamente adicionar um cabeçalho Content-Length à resposta. Isso substitui a configuração addContentLengthHeader que foi removida do Slim 3. Este middleware deve ser colocado no final da pilha de middlewares para que seja executado primeiro e encerrado por último.
Cook book
    Trailing / in routes    
        O Slim trata um padrão de URL com uma barra final como diferente de um sem barra final. Ou seja, /usuário e /usuário/ são diferentes e podem ter callbacks diferentes anexados.
        Para requisições GET, um redirecionamento permanente é aceitável, mas para outros métodos de requisição como POST ou PUT, o navegador enviará a segunda requisição com o método GET. Para evitar isso, basta remover a barra final e passar a URL manipulada para o próximo middleware.
    Retrieving Current Route
        Se precisar acessar a rota atual dentro de sua aplicação, será necessário instanciar o objeto RouteContext usando o ServerRequestInterface recebido.
        A partir daí, pode obter a rota via $routeContext->getRoute() e acessar o nome da rota usando getName() ou obter os métodos suportados por essa rota via getMethods(), etc.
        Observação: Se precisar acessar o objeto RouteContext durante o ciclo de middleware antes de chegar ao manipulador de rota, será necessário adicionar o Middleware de Roteamento como o middleware mais externo antes do middleware de tratamento de erros
    Using Doctrine with Slim
    Enabling CORS
    Uploading Files using POST forms
        Arquivos que são enviados usando formulários em requisições POST podem ser recuperados com o método getUploadedFiles() da classe Request.
        Ao enviar arquivos usando uma requisição POST, certifique-se de que o formulário de upload de arquivo tenha o atributo enctype="multipart/form-data", caso contrário getUploadedFiles() retornará um array vazio.
        Se vários arquivos forem enviados para o mesmo nome de entrada, adicione colchetes após o nome da entrada no HTML, caso contrário apenas um arquivo enviado será retornado para o nome da entrada pelo getUploadedFiles().
