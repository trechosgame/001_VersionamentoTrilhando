 - Aqui está a versão atualizada, limpa, profissional e mais robusta do seu script notaFinal. Mantive toda a funcionalidade original, mas apliquei boas práticas modernas do Unity (2023+):

 - Nome da classe mais descritivo: FinalScoreDisplay
 - Validação de referências no Awake
 - Uso de constantes para evitar "magic numbers"
 - Lógica de estrelas em método separado (fácil de manter)
 - Feedback visual mais claro no Inspector
 - Comentários XML para documentação
 - Proteção contra PlayerPrefs ausentes (valor padrão)
 - Preparado para expansão (ex: animação das estrelas, som de vitória)

 ~~
 using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

[RequireComponent(typeof(CanvasGroup))] // Opcional: garante que funcione em UI
public class FinalScoreDisplay : MonoBehaviour
{
    [Header("UI Elements")]
    [SerializeField] private Text scoreText;
    [SerializeField] private Text infoText;
    [SerializeField] private GameObject[] stars = new GameObject[3]; // 0 = 1ª estrela, 1 = 2ª, 2 = 3ª

    [Header("Configurações")]
    [SerializeField] private int minScoreFor1Star = 5;
    [SerializeField] private int minScoreFor2Stars = 7;
    [SerializeField] private int perfectScore = 10;

    [Header("PlayerPrefs Keys")]
    [SerializeField] private string themeIdKey = "idTema";
    [SerializeField] private string tempScoreKeyPrefix = "notaFinalTemp";
    [SerializeField] private string tempCorrectKeyPrefix = "acertosTemp";
    [SerializeField] private string scenePrefix = "T";

    private int currentThemeId;
    private int finalScore;
    private int correctAnswers;
    private int totalQuestions = 10; // Ajuste aqui ou receba dinamicamente

    private void Awake()
    {
        ValidateReferences();
    }

    private void Start()
    {
        LoadData();
        UpdateDisplay();
    }

    private void ValidateReferences()
    {
        if (scoreText == null) Debug.LogError("Score Text não atribuído!", this);
        if (infoText == null) Debug.LogError("Info Text não atribuído!", this);
        if (stars.Length != 3) Debug.LogWarning("Array de estrelas deve ter exatamente 3 elementos.", this);

        for (int i = 0; i < stars.Length; i++)
        {
            if (stars[i] == null)
                Debug.LogWarning($"Estrela {i + 1} não atribuída no array.", this);
        }
    }

    private void LoadData()
    {
        currentThemeId = PlayerPrefs.GetInt(themeIdKey, 0);

        string scoreKey = tempScoreKeyPrefix + currentThemeId;
        string correctKey = tempCorrectKeyPrefix + currentThemeId;

        finalScore = PlayerPrefs.GetInt(scoreKey, 0);
        correctAnswers = PlayerPrefs.GetInt(correctKey, 0);

        // Opcional: limpar temporários após uso (evita lixo acumulado)
        // PlayerPrefs.DeleteKey(scoreKey);
        // PlayerPrefs.DeleteKey(correctKey);
        // PlayerPrefs.Save();
    }

    private void UpdateDisplay()
    {
        if (scoreText != null)
            scoreText.text = finalScore.ToString();

        if (infoText != null)
            infoText.text = $"ACERTOU {correctAnswers} DE {totalQuestions}";

        UpdateStars(finalScore);
    }

    private void UpdateStars(int score)
    {
        HideAllStars();

        int starsEarned = 0;
        if (score >= perfectScore) starsEarned = 3;
        else if (score >= minScoreFor2Stars) starsEarned = 2;
        else if (score >= minScoreFor1Star) starsEarned = 1;

        for (int i = 0; i < starsEarned && i < stars.Length; i++)
        {
            if (stars[i] != null) stars[i].SetActive(true);
        }
    }

    private void HideAllStars()
    {
        foreach (var star in stars)
        {
            if (star != null) star.SetActive(false);
        }
    }

    /// <summary>
    /// Botão "Jogar Novamente" — carrega a mesma cena do tema atual
    /// </summary>
    public void PlayAgain()
    {
        string sceneName = scenePrefix + currentThemeId.ToString();

        if (SceneExists(sceneName))
        {
            SceneManager.LoadScene(sceneName);
        }
        else
        {
            Debug.LogError($"Cena '{sceneName}' não encontrada nos Build Settings.", this);
        }
    }

    private bool SceneExists(string sceneName)
    {
        for (int i = 0; i < SceneManager.sceneCountInBuildSettings; i++)
        {
            string path = SceneUtility.GetScenePathByBuildIndex(i);
            string name = System.IO.Path.GetFileNameWithoutExtension(path);
            if (name == sceneName) return true;
        }
        return false;
    }

    // Opcional: botão para voltar ao menu principal
    public void BackToMenu(string menuSceneName = "Menu")
    {
        if (SceneExists(menuSceneName))
            SceneManager.LoadScene(menuSceneName);
        else
            Debug.LogError($"Cena de menu '{menuSceneName}' não encontrada.", this);
    }
}
 ~~

 - Principais melhorias implementadas:
 - Nome da classe mais descritivo → FinalScoreDisplay
 - Validação robusta no Awake (evita erros silenciosos)
 - Constantes para pontuações (fácil ajustar critérios de estrelas)
 - Método separado para atualizar estrelas (sem repetição de código)
 - Verificação de existência da cena antes de carregar
 - PlayerPrefs com prefixos claros e opção de limpeza temporária comentada
 - Método BackToMenu pronto para uso (opcional)
 - Comentários XML para documentação (aparecem no IntelliSense)
 - Array de estrelas (loop simples, fácil de adicionar animação depois)

 - Como usar:

 - Coloque o script em um GameObject da tela de resultado (ex: Canvas ou painel final)
 - No Inspector:
 - Arraste os campos Score Text, Info Text e os 3 objetos de estrela no array Stars
 - Ajuste Total Questions se o número de questões mudar por tema

 - No botão "Jogar Novamente":
 -  On Click → arraste este objeto → escolha FinalScoreDisplay → PlayAgain

 - (Opcional) Botão "Voltar ao Menu":
 - On Click → FinalScoreDisplay → BackToMenu (passe o nome da cena do menu)

 - Sugestões para deixar ainda mais profissional (opcional):

 - Animação das estrelas:C#private void UpdateStars(int score)
~~
{
    HideAllStars();
    int starsEarned = /* cálculo */;
    for (int i = 0; i < starsEarned; i++)
    {
        if (stars[i] != null)
        {
            stars[i].SetActive(true);
            // Exemplo com DOTween:
            // stars[i].transform.localScale = Vector3.zero;
            // stars[i].transform.DOScale(1f, 0.5f).SetDelay(i * 0.2f);
        }
    }
}
~~
- Som de vitória:C#private void Start()
~~
{
    LoadData();
    UpdateDisplay();
    if (finalScore >= perfectScore)
        AudioManager.Instance?.PlayCorrect(); // ou som de vitória

}
~~
Transição suave para próxima cena:
Use LoadSceneAsync + barra de loading (posso adicionar se quiser)

Teste e me diga se precisa de mais ajustes (ex: fade, som, animação das estrelas, integração com menu de temas, etc.)! 🚀
