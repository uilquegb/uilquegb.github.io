---
layout: post
title: Poucas linhas, pouco código e muita utilidade
---

Uma das coisas mais estranhas que aprendi até agora (pelo menos no momento do aprendizado) foi o LINQ (Query Expression Syntax). A ideia de mudar completamente a sintaxe de um código em C# me assustou bastante. Porém, não demorou muito para eu encontrar alguns benefícios e tirar proveito disso.

## Diferenças

O _LINQ_ pode ser usado de basicamente duas formas:

1. Fluent Syntax
2. Query Expression

Enquanto a primeira é mais comum, por se tratar métodos extensíveis, muitos profissionais da área já a usam sem nem saber, a segunda dá mais indícios de se tratar de uma tecnologia diferente e com sua própria sintaxe.

Abaixo, você consegue ver a diferença entre os dois modos de usar _LINQ_:

1. Fluent Syntax

```csharp
private IEnumerable<string> nomes = new List<string>(){ "Uilque", "Lucas", "Luiz", "João", "Pedro", "Antônio" };

public IEnumerable<string> NomesGrandesEmCaixaAltaOrdenados()
{
    var nomesGrandesEmCaixaAlta = nomes.Where(n => n.Length > 5)
                                       .OrderBy(n => n)
                                       .Select(n => n.ToUpper());
    return nomesGrandesEmCaixaAlta;
}
```

2. Query Expression

```csharp
private IEnumerable<string> nomes = new List<string>(){ "Uilque", "Lucas", "Luiz", "João", "Pedro", "Antônio" };

public IEnumerable<string> NomesGrandesEmCaixaAltaOrdenados()
{
    var nomesGrandesEmCaixaAlta = from n in nomes
                                  orderby n
                                  where n.Length > 5
                                  select n.ToUpper();
    return nomesGrandesEmCaixaAlta;
}
```

Você ainda pode fazer um mix dos dois modos sem nenhum problema, até porque nem todos métodos do _LINQ Fluent Syntax_ tem um correspondente na outra sintaxe.

```csharp
private IEnumerable<string> nomes = new List<string>(){ "Uilque", "Lucas", "Luiz", "João", "Pedro", "Antônio" };

public IEnumerable<string> NomesGrandesEmCaixaAltaOrdenados1()
{
    var nomesGrandesEmCaixaAlta = from n in nomes.Where(n => n.Length > 5)
                                  orderby n
                                  select n.ToUpper();
    return nomesGrandesEmCaixaAlta;
}

public IEnumerable<string> NomesGrandesEmCaixaAltaOrdenados2()
{
    var nomesGrandesEmCaixaAlta = (from n in nomes.Where(n => n.Length > 5)
                                   select n.ToUpper()
                                  ).OrderBy(n => n);
    return nomesGrandesEmCaixaAlta;
}

public IEnumerable<string> NomesGrandesEmCaixaAltaOrdenados3()
{
    var nomesGrandesEmCaixaAlta = (from n in nomes
                                   where n.Length > 5
                                   select n
                                  ).OrderBy(n => n)
                                   .Select(n => n.ToUpper());
    return nomesGrandesEmCaixaAlta;
}
```

## Como aplicar em casos do dia-a-dia?

Os exemplos acima ilustram muito bem a forma como o _LINQ_ pode ser escrito, mas vamos partir para um exemplo real e que pode ser usado no dia-a-dia?

### Gerar senhas aleatórias com poucas linhas

Sabemos que essa não é a melhor forma, nem a mais segura de gerar uma senha, mas está bem claro que é muito simples e de baixíssima complexidade, principalmente por utilizar apenas algumas linhas para gerar senhas aleatórias, podendo ainda delimitar a quantidade de caracteres nesta senha.

```csharp
public string GeraSenhaAleatoria(int tamanhoSenha)
{
    char[] possiveisCaracteres = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$&".ToArray();
  
    // criar um IEnumerable<bool> com o total de itens definido tamanhoSenha
    string senha = Enumerable.Repeat(true, tamanhoSenha)
    // Seleciona, randomicamente, alguns caracteres e retorna um IEnumerable<char> com caracteres aleatórios
                             .Select(c => possiveisCaracteres[rnd.Next(possiveisCaracteres.Length)])
    // Junta todos os caracteres em uma única string
                             .Aggregate(String.Empty, (current, next) => current.ToString() + next.ToString());
    return senha;
}
```

### Converter listas completas com itens complexos

Quando trabalhamos com uma linguagem Orientada à Objetos quase sempre utilizamos os conceitos de polimorfismo e herança, mas na hora de converter dados de um tipo para outro pode gerar uma dor de cabeça, ainda mais quando se trata de uma coleção de itens. Mas não se preocupe, o LINQ pode te ajudar com isso também.

```csharp
public List<object> TentaConverterListaDeStringParaObjeto(List<string> listaDeStrings)
{
    // Não vai compilar
    return (List<object>)listaDeStrings;
}

public List<string> TentaConverterListaDeObjetoParaString(List<object> listaDeObjetos)
{
    // Não vai compilar
    return (List<string>)listaDeObjetos;
}


public List<object> ConverteStringParaObjetos(List<string> listaDeStrings)
{
    // retorna todos os itens como object
    return listaDeStrings.Cast<object>().ToList();
}

public List<string> TentaConverterStringEmListaDeObjetos(List<object> listaDeObjetos)
{
    // Converte lista, se tiver algum iten que não é String, será lançado uma exceção (InvalidCastException)
    return listaDeObjetos.Cast<string>().ToList();
}

public List<string> FiltraStringEmListaDeObjetos(List<object> listaDeObjetos)
{
    // Retorna apenas os itens do tipo string, os outros serão ignorados
    return listaDeObjetos.OfType<string>().ToList();
}
```

### Iterar em mais de uma lista sem utilizar mais de um for

Adivinha quem pode te dar uma mão na hora de iterar em duas ou mais coleções diferentes, com itens do mesmo tipo e com a intenção de executar a mesma tarefa para todos os itens?

```csharp
private List<string> nomeFuncionariosSede = new List<string>(){ "Uilque", "Lucas", "Luiz", "João" };
private List<string> nomeFuncionariosFiliais = new List<string>(){ "Maria", "Paula", "Pedro", "Antõnio"};

public void IterarEmDuasListasDoMesmoTipoComForeach()
{
    foreach(string funcionario in nomeFuncionariosSede)
    {
        Console.WriteLine(funcionario);
    }
  
    foreach(string funcionario in nomeFuncionariosFiliais)
    {
        Console.WriteLine(funcionario);
    }
}

public void IterarEmDuasListasDoMesmoTipoComLINQ()
{
    foreach(string funcionario in nomeFuncionariosSede.Concat(nomeFuncionariosFiliais))
    {
        Console.WriteLine(funcionario);
    }
}
```

### Gerar e/ou inicializar coleções

Sabemos que existem diversas bibliotecas de _Mock_ por aí, porém você sempre precisa criar aquele `for` para inicializar uma coleção ou, pior ainda, inicializá-la “na unha”. Existe um jeito melhor de fazer isso :smile:

```csharp
public int[] novoArrayComItensRepetidos(int itemARepetir, int totalItens)
{
    return Enumerable.Repeat(itemARepetir, totalItens).ToArray();
}

public int[] novoArraySequencial(int ultimoValor)
{
    return Enumerable.Range(1, ultimoValor).ToArray();
}

public int[] novoArraySequencialMultiploDe10(int totalItens)
{
    return Enumerable.Range(1, totalItens).Select(n => n * 10).ToArray();
}
```
Por ora é só, espero que vocẽ tenha gostado.

Qualquer dúvida, sugestão ou crítica construtiva, fique a vontade para comentar :wink: