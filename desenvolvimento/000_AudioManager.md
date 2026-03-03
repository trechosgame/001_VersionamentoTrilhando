using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;
using System;

[RequireComponent(typeof(CanvasGroup))] // Opcional: garante que o componente funcione corretamente
public class ThemeSelector : MonoBehaviour
{
    [Header("UI Elements")]
    [SerializeField] private Button playButton;
    [SerializeField] private Text themeNameText;
    [SerializeField] private GameObject themeInfoPanel;
    [SerializeField] private Text themeInfoText;
    [SerializeField] private GameObject[] starObjects = new GameObject[3]; // Array para estrelas (0-2)

    [Header("Theme Data")]
    [SerializeField] private ThemeData[] themes; // ScriptableObject ou struct para dados dos temas

    [Header("PlayerPrefs Keys")]
    [SerializeField] private string themeIndexKey = "SelectedThemeIndex";
    [SerializeField] private string finalScoreKeyPrefix = "FinalScore_";
    [SerializeField] private string correctAnswersKeyPrefix = "CorrectAnswers_";
    [SerializeField] private string scenePrefix = "T";

    [System.Serializable]
    private struct ThemeData
    {
        public string name;
        public int totalQuestions;
    }

    private int currentThemeIndex;
    private const int MAX_STARS = 3;
    private const float MIN_SCORE_FOR_1_STAR = 5f;
    private const float MIN_SCORE_FOR_2_STARS = 7f;
    private const float MIN_SCORE_FOR_3_STARS = 10f;

    private void Start()
    {
        ValidateReferences();
        InitializeUI();
        LoadSavedTheme();
    }

    private void ValidateReferences()
    {
        if (playButton == null) Debug.LogError("Play Button não atribuído!", this);
        if (themeNameText == null) Debug.LogError("Theme Name Text não atribuído!", this);
        if (themeInfoPanel == null) Debug.LogError("Theme Info Panel não atribuído!", this);
        if (starObjects.Length != MAX_STARS)
            Debug.LogWarning($"Array de estrelas deve ter exatamente {MAX_STARS} elementos!", this);
    }

    private void InitializeUI()
    {
        currentThemeIndex = 0;
        themeNameText.text = themes[currentThemeIndex].name;
        themeInfoText.text = "Você acertou X de X questões.";
        themeInfoPanel.SetActive(false);
        playButton.interactable = false;
        HideAllStars();
    }

    public void SelectTheme(int themeIndex)
    {
        if (themeIndex < 0 || themeIndex >= themes.Length)
        {
            Debug.LogWarning($"Tema inválido: {themeIndex}. Usando tema padrão 0.");
            themeIndex = 0;
        }

        currentThemeIndex = themeIndex;
        PlayerPrefs.SetInt(themeIndexKey, currentThemeIndex);

        UpdateThemeDisplay();
        LoadAndDisplayScore();
        themeInfoPanel.SetActive(true);
        playButton.interactable = true;
    }

    private void UpdateThemeDisplay()
    {
        themeNameText.text = themes[currentThemeIndex].name;
    }

    private void LoadAndDisplayScore()
    {
        string scoreKey = finalScoreKeyPrefix + currentThemeIndex;
        string correctKey = correctAnswersKeyPrefix + currentThemeIndex;

        int finalScore = PlayerPrefs.GetInt(scoreKey, 0); // Default 0 se não existir
        int correctAnswers = PlayerPrefs.GetInt(correctKey, 0);

        int totalQuestions = themes[currentThemeIndex].totalQuestions;
        themeInfoText.text = $"Você acertou {correctAnswers} de {totalQuestions} questões!";

        UpdateStars(finalScore);
    }

    private void UpdateStars(int score)
    {
        HideAllStars();

        int starsEarned = 0;
        if (score >= MIN_SCORE_FOR_3_STARS) starsEarned = 3;
        else if (score >= MIN_SCORE_FOR_2_STARS) starsEarned = 2;
        else if (score >= MIN_SCORE_FOR_1_STAR) starsEarned = 1;

        for (int i = 0; i < starsEarned && i < starObjects.Length; i++)
        {
            if (starObjects[i] != null) starObjects[i].SetActive(true);
        }
    }

    private void HideAllStars()
    {
        foreach (var star in starObjects)
        {
            if (star != null) star.SetActive(false);
        }
    }

    public void PlayTheme()
    {
        string sceneName = scenePrefix + currentThemeIndex.ToString();
        SceneManager.LoadScene(sceneName);
    }

    private void LoadSavedTheme()
    {
        if (PlayerPrefs.HasKey(themeIndexKey))
        {
            int savedIndex = PlayerPrefs.GetInt(themeIndexKey);
            SelectTheme(savedIndex);
        }
    }

    private void OnDestroy()
    {
        // Opcional: Salvar estado ao destruir (útil para cenas dinâmicas)
        PlayerPrefs.Save();
    }
}


