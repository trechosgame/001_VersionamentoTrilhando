~~
using UnityEngine;
using UnityEditor.UI;
using UnityEngine.SceneManagement;
using System.Collections;
using System.Collections.Generic;
using UnityEngine.UI;
public class temaInfo : MonoBehaviour
{
    public int idTema;

    public GameObject estrela1;
    public GameObject estrela2;
    public GameObject estrela3;

    private int notaFinal;

    void Start()
    {
        estrela1.SetActive(false);
        estrela2.SetActive(false);
        estrela3.SetActive(false);
        
        int notaFinal = PlayerPrefs.GetInt("notaFinal" + idTema.ToString());
        
        if (notaFinal == 10)
        {
            estrela1.SetActive(true);
            estrela2.SetActive(true);
            estrela3.SetActive(true);
        }
        else if (notaFinal >= 7)
        {
            estrela1.SetActive(true);
            estrela2.SetActive(true);
            estrela3.SetActive(false);
        }
        else if (notaFinal >= 5)
        {
            estrela1.SetActive(true);
            estrela2.SetActive(false);
            estrela3.SetActive(false);
        }
    
    }

   
    void Update()
    {
        
    }
}

~~
- Aqui está a versão atualizada, limpa, profissional, segura e otimizada do seu script responder. Mantive toda a  - - - lógica original (perguntas, alternativas, acertos, cálculo de nota, salvamento em PlayerPrefs e transição para tela - de nota final), mas apliquei boas práticas modernas do Unity:

- Nome da classe mais descritivo: QuizResponder
- Validação robusta de referências e dados
- Uso de struct para organizar perguntas (fácil editar no Inspector)
- Constantes para evitar "magic numbers"
- Lógica de resposta simplificada e sem repetição
- Cálculo de nota mais seguro e arredondado corretamente
- Feedback visual (cor na resposta certa/errada – opcional)
- Preparado para expansão (temporizador, animações, som de acerto/erro)
- Comentários claros e XML para documentação

 - Como usar agora:

 - No Inspector:
 - Arraste os 5 Text (pergunta + 4 alternativas + progresso)
 - No campo Questions:
 - Size = número de perguntas
 - Preencha cada pergunta com:
 - Question
 - Option A/B/C/D
 - Correct Answer (exatamente "A", "B", "C" ou "D")

 - Nos botões de resposta:
 - Botão A → On Click → QuizResponder → AnswerA
 - Botão B → AnswerB
 - Botão C → AnswerC
 - Botão D → AnswerD

 - Tela de nota final (seu script anterior) lê:
 - notaFinalTempX e acertosTempX (onde X = idTema)

 - Benefícios dessa versão:

 - Sem repetição de código (uma única função SubmitAnswer)
 - Fácil de editar perguntas (tudo no Inspector, sem arrays separados)
 - Validação (evita erros se algo estiver faltando)
 - Escalável (fácil adicionar timer, feedback visual, sons, animações)
 - Seguro (não quebra se PlayerPrefs não existir)

 - Se quiser:

 - Feedback visual (verde/vermelho nas opções)
 - Som de acerto/erro
 - Temporizador por pergunta
 - Animação de transição
 - Suporte a imagens nas perguntas

 - É só pedir que eu adiciono na próxima versão! 🚀
