# Dashboard Power BI Embedado + Refresh Automático

Este projeto consiste em uma página HTML simples que **embuta (embed)** um dashboard do Power BI via `iframe` e o **recarrega automaticamente a cada 3 minutos**, mantendo o conteúdo do painel atualizado mesmo em um site estático hospedado no GitHub Pages.

## O que esse código faz

- Renderiza um iframe apontando para a URL de visualização de um relatório do Power BI (`https://app.powerbi.com/view?r=...`).
- Ajusta automaticamente o tamanho do iframe para preencher 100% da altura da tela, evitando cortes no topo e no rodapé.
- Aplica um **refresh automático a cada 3 minutos** (180.000 ms), forçando o recarregamento do iframe sem atualizar a página inteira.
- É pensado para funcionar diretamente no **GitHub Pages** (sem backend), apenas com arquivos estáticos (`index.html`).

## Como usar

1. Suba este arquivo `index.html` para um repositório público no GitHub.
2. Habilite o GitHub Pages em **Settings → Pages**, apontando o source para a branch `main` e pasta `/root`.
3. Acesse o dashboard pela URL gerada, por exemplo:  
   `https://andrade-gabriel-urbam.github.io/dashboard-grupourbam`
4. O dashboard do Power BI será recarregado **a cada 3 minutos** enquanto a página estiver aberta.

---

## Código fonte (`index.html`)

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

// reload do iframe a cada 3 minutos (180.000 ms)
const srcOriginal = document.getElementById("powerbi").src;
setInterval(() => {
    const iframe = document.getElementById("powerbi");
    iframe.src = "";           // limpa primeiro
    setTimeout(() => {
        iframe.src = srcOriginal; // volta a URL original
    }, 100);
}, 180000);
</script>

</body>
</html>
```

## Observações

- Se o Power BI exige login ou compartilhamento restrito, o usuário precisa estar autenticado no Power BI na mesma sessão do navegador.
- Este sistema é útil para telões ou dashboards sempre abertos, sem necessidade de recarregar manualmente a página.
