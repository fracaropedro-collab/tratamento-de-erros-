# tratamento-de-erros
1. Tratamento de erros e exceções

Tratamento de erros é o conjunto de práticas para prever, detectar e responder a situações anômalas durante a execução de um programa, evitando falhas abruptas.

Exceção é um objeto lançado em tempo de execução quando ocorre uma condição anormal, interrompendo o fluxo normal até ser capturado por um catch.

Erro x exceção: erro é um termo amplo (pode ser de sintaxe, lógica ou execução); exceção é o mecanismo específico, em tempo de execução, usado para sinalizar e tratar um erro de forma estruturada (throw/catch).

Importância: evita que o programa quebre, melhora a experiência do usuário, facilita a depuração e garante liberação correta de recursos.

typescript
function dividir(a: number, b: number): number {
  if (b === 0) throw new Error("Divisão por zero não é permitida.");
  return a / b;
}

try {
  console.log(dividir(10, 0));
} catch (erro) {
  console.error("Erro:", (erro as Error).message);
}
2. Tratamento de exceções

Sua finalidade é separar o fluxo normal do fluxo de erro, evitando ifs repetidos e permitindo tratar falhas em um ponto centralizado, mesmo quando ocorrem em pontos profundos do código.

typescript
function buscarUsuario(id: number) {
  const usuarios = [{ id: 1, nome: "Ana" }];
  const usuario = usuarios.find((u) => u.id === id);
  if (!usuario) throw new Error(`Usuário ${id} não encontrado.`);
  return usuario;
}

try {
  buscarUsuario(5);
} catch (erro) {
  console.error("Falha:", (erro as Error).message);
}

O try executa o código arriscado; como o usuário não existe, a exceção é lançada e capturada pelo catch, que trata o problema sem interromper o programa.

3. try, catch e finally
try: delimita o trecho que pode gerar exceção.
catch: captura e trata a exceção lançada.
finally: sempre executa, com ou sem erro (ideal para liberar recursos).
typescript
try {
  const config = JSON.parse("{ nome: 'sem aspas' }"); // JSON inválido
  console.log(config);
} catch (erro) {
  console.error("Erro ao ler configuração:", (erro as Error).message);
} finally {
  console.log("Processo encerrado.");
}

O try tenta interpretar um JSON inválido, o catch captura a falha e trata, e o finally roda sempre, independente do resultado.

4. throw

throw lança manualmente uma exceção quando o código identifica uma condição inválida, interrompendo a função atual e propagando o erro até um catch.

typescript
class IdadeInvalidaError extends Error {
  constructor(idade: number) {
    super(`Idade inválida: ${idade}.`);
    this.name = "IdadeInvalidaError";
  }
}

function validarIdade(idade: number): void {
  if (idade < 0 || idade > 120) throw new IdadeInvalidaError(idade);
  console.log("Idade válida.");
}

try {
  validarIdade(-5);
} catch (erro) {
  console.error((erro as Error).message);
}

A função valida a idade; se inválida, lança a exceção personalizada, que é capturada e tratada no catch.

5. Aplicação prática — Transferência bancária
typescript
class ValorInvalidoError extends Error {}
class SaldoInsuficienteError extends Error {}

interface Conta {
  titular: string;
  saldo: number;
}

function transferir(origem: Conta, destino: Conta, valor: number): void {
  try {
    if (valor <= 0) throw new ValorInvalidoError("Valor deve ser maior que zero.");
    if (valor > origem.saldo) throw new SaldoInsuficienteError("Saldo insuficiente.");

    origem.saldo -= valor;
    destino.saldo += valor;
    console.log(`Transferência de R$ ${valor} concluída.`);
  } catch (erro) {
    console.error("Transferência não realizada:", (erro as Error).message);
  } finally {
    console.log("Processo finalizado.\n");
  }
}

const contaA: Conta = { titular: "Ana", saldo: 500 };
const contaB: Conta = { titular: "Bruno", saldo: 100 };

transferir(contaA, contaB, -50);   // erro: valor inválido
transferir(contaA, contaB, 1000);  // erro: saldo insuficiente
transferir(contaA, contaB, 200);   // sucesso

A função valida o valor (rejeita ≤ 0) e o saldo (rejeita valor maior que o disponível), lançando exceções específicas para cada caso. O catch trata os erros com mensagens claras, e o finally garante que o encerramento do processo sempre seja registrado.
