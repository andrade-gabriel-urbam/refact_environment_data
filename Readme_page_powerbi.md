# Dashboard Power BI Embedado + Refresh Automático

Este projeto consiste em um conjunto de páginas HTML simples que **embutem (embed)** dashboards do Power BI via `iframe` em um site estático hospedado no GitHub Pages, com diferentes comportamentos de atualização.

## Páginas disponíveis

Hoje são três páginas distintas servidas pelo mesmo repositório:

- `index.html` → painel único com **refresh automático a cada 3 horas**.
- `dashboard-powerbi-1.html` → painel único adicional (mesmo modelo, para uso separado).
- `dashboard-powerbi-loop.html` → dois dashboards do Power BI **alternando a cada 60 segundos** entre si.

Cada uma é acessível diretamente pela URL do GitHub Pages com o nome do arquivo.

---

## O que esses códigos fazem

- Renderizam um `iframe` apontando para a URL de visualização de um relatório do Power BI (`https://app.powerbi.com/view?r=...`).
- Ajustam automaticamente o tamanho do iframe para preencher 100% da altura da tela, evitando cortes no topo e no rodapé.
- Em `index.html` e `dashboard-powerbi-1.html`:
  - Aplicam **refresh automático a cada 3 horas** (10.800.000 ms), recarregando o iframe sem atualizar a página inteira.
- Em `dashboard-powerbi-loop.html`:
  - Alternam entre **duas URLs distintas do Power BI** a cada 60 segundos, criando um “carousel” de dashboards.
- Todos funcionam diretamente no **GitHub Pages** (sem backend), apenas com arquivos estáticos.

---

## Como usar

1. Suba os arquivos `index.html`, `dashboard-powerbi-1.html` e `dashboard-powerbi-loop.html` para o repositório público no GitHub.
2. Habilite o GitHub Pages em **Settings → Pages**, apontando o source para a branch `main` e pasta `/root`.
3. Acesse cada página pela URL correspondente, por exemplo:

   - Página inicial com refresh a cada 3h:  
     `https://andrade-gabriel-urbam.github.io/dashboard-grupourbam/`

   - Dashboard 1 estático (se quiser usar o mesmo modelo):  
     `https://andrade-gabriel-urbam.github.io/dashboard-grupourbam/dashboard-powerbi-1.html`

   - Dashboard alternando entre 2 telas a cada 60 segundos:  
     `https://andrade-gabriel-urbam.github.io/dashboard-grupourbam/dashboard-powerbi-loop.html`

4. Os dashboards do Power BI serão recarregados ou alternados conforme o comportamento de cada página enquanto a URL estiver aberta.

---

## Código fonte (`index.html` - dashboard único com refresh a cada 3h)

```html
<!DOCTYPE html>
<html>
<body style="margin:0; overflow:hidden;">

<div id="container" style="width:100%; height:100vh; overflow:hidden; position:relative;">
    
    <iframe 
        id="powerbi"
        src="https://app.powerbi.com/view?r=eyJrIjoiZGU5YzRhYjItOTk0ZC00NzI0LWJkZTUtN2U3NDgyNjgwZGQwIiwidCI6ImQ3NzhiYWZkLWUxYTgtNGU4OS05M2E2LWRiZjU0YjY5YjkzMCJ9"
        style="position:absolute; left:0; width:100%; border:none;">
    </iframe>

</div>

<script>
function ajustar() {
    const iframe = document.getElementById("powerbi");
    const altura = window.innerHeight;

    iframe.style.height = (altura * 1.08) + "px";
    iframe.style.top = (altura * -0.01) + "px";
}

// ajusta na abertura e no resize
ajustar();
window.addEventListener("resize", ajustar);

// reload do iframe a cada 3 horas (10.800.000 ms)
const srcOriginal = document.getElementById("powerbi").src;
setInterval(() => {
    const iframe = document.getElementById("powerbi");
    iframe.src = "";           // limpa primeiro
    setTimeout(() => {
        iframe.src = srcOriginal; // volta a URL original
    }, 100);
}, 10800000);
</script>

</body>
</html>
```

---

## Código fonte (`dashboard-powerbi-loop.html` - 2 dashboards alternando)

```html
<!DOCTYPE html>
<html>
<body style="margin:0; overflow:hidden;">

<div id="container" style="width:100%; height:100vh; overflow:hidden; position:relative;">
    
    <iframe 
        id="powerbi"
        src="https://app.powerbi.com/view?r=eyJrIjoiODBlYTk2NTUtYjVkOC00MGUyLWJmMDUtZjZjOTc5YWJhNzExIiwidCI6ImQ3NzhiYWZkLWUxYTgtNGU4OS05M2E2LWRiZjU0YjY5YjkzMCJ9&pageName=a068728ed947ecb9abdb"
        style="position:absolute; left:0; width:100%; border:none;">
    </iframe>

</div>

<script>
// URLs dos dois dashboards do Power BI
const URLs = [
    "https://app.powerbi.com/view?r=eyJrIjoiODBlYTk2NTUtYjVkOC00MGUyLWJmMDUtZjZjOTc5YWJhNzExIiwidCI6ImQ3NzhiYWZkLWUxYTgtNGU4OS05M2E2LWRiZjU0YjY5YjkzMCJ9&pageName=a068728ed947ecb9abdb",
    "https://app.powerbi.com/view?r=eyJrIjoiODBlYTk2NTUtYjVkOC00MGUyLWJmMDUtZjZjOTc5YWJhNzExIiwidCI6ImQ3NzhiYWZkLWUxYTgtNGU4OS05M2E2LWRiZjU0YjY5YjkzMCJ9&pageName=04f2441200b81e810ca8"
];

let indiceAtual = 0;

function ajustar() {
    const iframe = document.getElementById("powerbi");
    const altura = window.innerHeight;

    iframe.style.height = (altura * 1.08) + "px";
    iframe.style.top = (altura * -0.01) + "px";
}

ajustar();
window.addEventListener("resize", ajustar);

// Troca de dashboard a cada 60 segundos
setInterval(() => {
    indiceAtual = (indiceAtual + 1) % URLs.length; // avança + volta para o primeiro
    const iframe = document.getElementById("powerbi");
    iframe.src = ""; // limpa o iframe
    setTimeout(() => {
        iframe.src = URLs[indiceAtual]; // troca para o próximo dashboard
    }, 100);
}, 60000);
</script>

</body>
</html>
```

---

## Observações

- Se o Power BI exige login ou compartilhamento restrito, o usuário precisa estar autenticado no Power BI na mesma sessão do navegador.
- Essas páginas são ideais para telões ou dashboards sempre abertos, sem necessidade de recarregar manualmente.
- Você pode criar mais páginas seguindo o mesmo padrão e acessá‑las pela URL completa com o nome do arquivo.
