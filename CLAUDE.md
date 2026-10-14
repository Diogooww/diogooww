# Contexto para o Claude

## Obsidian no Google Drive

O vault do Obsidian do Diogo fica no Google Drive (conector Google Drive):

```
Obsidian/                          id 14X3msS1fwNY9VAcA7P2Di16E6UCHslL_
  Knowledge Base/                  id 1A5JnHYED1j2SYTaWsxppdFWoiiUXJ0p7  (raiz do vault)
    01-projects/                   id 1VzG8mIeVqB5oFQ9fFWoEAt_jWlTRgVUn
      DTA - Robos de Pedidos/      id 14KVoTt3ABN0ygfBNUpvuQ8pAKY9zGHJY
```

Para listar uma pasta: `search_files` com `parentId = '<id>'`.

### Projeto DTA - Robos de Pedidos

Robos em Python que leem pedidos de clientes (PDF/Excel), montam JSON e enviam para a API do Protheus.

Ao retomar o projeto, ler primeiro:

1. `current-context.md` (raiz) - estado atual e proximo passo
2. `pendencias/pendencias-dta.md` - lista de pendencias
3. `index.md` (raiz) - indice das demais notas

Subpastas: `arquitetura/`, `decisoes-tecnicas/`, `implementacoes/`, `bugs/`, `pendencias/`. Raiz tambem tem `README.md` e `session-log.md`.

Regras registradas nas notas: o usuario escreve o codigo e a IA guia e revisa (so edita quando pedido direto); IA nao le `.env`/`cofre.env` nem arquivos de pedido; nomes de codigo em ingles, mensagens em portugues.

### Padrao do JSON do pedido (decidido 2026-10-14)

Detalhes em `decisoes-tecnicas/padrao-campos-json.md`. Vale para robo novo e robo refeito.

- `CNPJBunzl`: ler do PDF, no campo do fornecedor (CNPJ do fornecedor). Nunca constante fixa.
- `CNPJEntrega`: nunca enviar, sempre `""`.

Exemplo de robo de PDF: `implementacoes/gkt-exemplo-robo-pdf.md`.

### Padrao de codigo limpo dos robos (decidido 2026-10-14)

Detalhes em `decisoes-tecnicas/padrao-codigo-limpo.md`. Modelo: `novo_cliente.py`. Aplicar em todo codigo novo ou refeito.

- Ordem do arquivo: imports (3 blocos) -> constantes agrupadas por secao -> `LayoutError` -> entrada + ajudantes (`is_ready`, `reserve`, `send`) -> `read_*` -> `build_json` -> `process` + `check_*` -> apoio -> `__main__` chamando `run_test_mode()`.
- Funcao pequena com uma responsabilidade e nome com verbo; quebrar se passar de ~25 linhas. `continue`/`return` cedo em vez de aninhar.
- Nomes em ingles, mensagens em portugues; sem nome generico (`values`, `data`) nem abreviacao.
- Sem numero/texto magico (constante com nome); sem duplicacao (constante ou funcao); regex compilada em constante com comentario de exemplo por parte.
- Docstring de uma linha com exemplo entrada -> saida; comentario explica o porque.
- Erros: juntar todos antes do `LayoutError`; mensagem com campo/aba/celula; `except` so especifico; layout errado -> `Pedido com erro` + `.erros.txt`. Nunca pular dado sem avisar.
- `pathlib`; `datetime.now()` (nunca `utcnow`); `# CONFERIR:` no que falta confirmar; refatorar nao muda regra de negocio.
