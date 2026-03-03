- Aqui está a versão atualizada, limpa, profissional e mais robusta do seu script comandosBasicos. Mantive toda a funcionalidade original, mas apliquei boas práticas modernas do Unity (2023+):

 - Nomenclatura clara e em português/inglês consistente
 - Validação e mensagens de erro úteis
 - Comentários XML para documentação
 - Proteção contra carregamento de cenas inválidas
 - Opções de confirmação para reset (evita reset acidental em produção)
 - Método de transição de cena mais flexível (com callback opcional)
 - Preparado para expansão (ex: fade, loading screen, async loading)

 ~~
 using UnityEngine;
using UnityEngine.SceneManagement;
using System;

public class SceneCommands : MonoBehaviour
{
    [Header("Configurações")]
    [SerializeField] private bool showResetConfirmation = true; // Mostra diálogo de confirmação no reset
    [SerializeField] private string confirmationMessage = "Tem certeza que deseja resetar TODAS as pontuações?\nIsso não pode ser desfeito!";

    /// <summary>
    /// Carrega uma cena pelo nome.
    /// </summary>
    /// <param name="sceneName">Nome exato da cena no Build Settings</param>
    public void LoadScene(string sceneName)
    {
        if (string.IsNullOrWhiteSpace(sceneName))
        {
            Debug.LogError("Nome da cena não pode ser vazio ou nulo.", this);
            return;
        }

        if (!SceneExists(sceneName))
        {
            Debug.LogError($"Cena '{sceneName}' não encontrada nos Build Settings.", this);
            return;
        }

        try
        {
            SceneManager.LoadScene(sceneName);
        }
        catch (Exception ex)
        {
            Debug.LogError($"Erro ao carregar cena '{sceneName}': {ex.Message}", this);
        }
    }

    /// <summary>
    /// Carrega cena de forma assíncrona (ideal para loading screens).
    /// </summary>
    public AsyncOperation LoadSceneAsync(string sceneName)
    {
        if (!SceneExists(sceneName))
        {
            Debug.LogError($"Cena '{sceneName}' não encontrada.", this);
            return null;
        }

        return SceneManager.LoadSceneAsync(sceneName);
    }

    /// <summary>
    /// Reseta todas as PlayerPrefs (com confirmação opcional).
    /// </summary>
    public void ResetAllScores()
    {
        if (showResetConfirmation)
        {
            // Em produção você pode usar um popup UI em vez de Debug.Log
            if (!UnityEditor.EditorUtility.DisplayDialog("Confirmação", confirmationMessage, "Sim", "Não"))
            {
                Debug.Log("Reset cancelado pelo usuário.");
                return;
            }
        }

        PlayerPrefs.DeleteAll();
        PlayerPrefs.Save(); // Garante que o reset seja salvo imediatamente

        Debug.Log("Todas as PlayerPrefs foram resetadas com sucesso.");
    }

    /// <summary>
    /// Verifica se a cena existe nos Build Settings.
    /// </summary>
    private bool SceneExists(string sceneName)
    {
        for (int i = 0; i < SceneManager.sceneCountInBuildSettings; i++)
        {
            string path = SceneUtility.GetScenePathByBuildIndex(i);
            string name = System.IO.Path.GetFileNameWithoutExtension(path);
            if (name == sceneName)
                return true;
        }
        return false;
    }

    // Exemplos de uso comum (você pode chamar de botões ou outros scripts)
    public void LoadMainMenu() => LoadScene("MenuPrincipal");
    public void LoadGame()     => LoadScene("Jogo");
    public void QuitGame()
    {
#if UNITY_EDITOR
        UnityEditor.EditorApplication.isPlaying = false;
#else
        Application.Quit();
#endif
    }

    // Para debug ou testes rápidos
    [ContextMenu("Reset Scores (Editor Only)")]
    private void ResetScoresEditor()
    {
        ResetAllScores();
    }
}
~~

 - Principais melhorias feitas:

 - Nome da classe mais descritivo: SceneCommands (em vez de comandosBasicos)
 - Validação robusta:
 - Verifica se a cena existe antes de tentar carregar
 - Evita erros silenciosos com Debug.LogError

 - Confirmação de reset (evita apagar dados por acidente)
 - PlayerPrefs.Save() explícito após reset
 - Método assíncrono LoadSceneAsync (pronto para loading screens)
 - Métodos de conveniência (LoadMainMenu, QuitGame)
 - [ContextMenu] para testar reset direto no Inspector (útil em desenvolvimento)
 - Comentários XML para documentação (aparecem no IntelliSense)
 - Uso de #if UNITY_EDITOR para quit funcionar no editor

 - Como usar agora:

 - Coloque o script em um GameObject persistente (ou no Canvas principal)
 - No botão (no Inspector → On Click):
 - Arraste o GameObject com esse script
 - Escolha SceneCommands → LoadScene
 - Digite o nome da cena (ex: "Menu", "Jogo", "Creditos")

 - Para reset:
 - Botão "Resetar Pontuações" → SceneCommands → ResetAllScores

-  Para sair do jogo:
 - Botão "Sair" → SceneCommands → QuitGame

 - Dicas para deixar ainda mais profissional:

 - Adicione fade/transition:C#public void LoadSceneWithFade(string sceneName)
{
    // Exemplo com DOTween ou seu sistema de fade
    // FadeOut();
    LoadScene(sceneName);
}
 - Salve apenas chaves específicas (em vez de DeleteAll):C#public void ResetThemeScores(int themeId)
{
    PlayerPrefs.DeleteKey($"notaFinal{themeId}");
    PlayerPrefs.DeleteKey($"acertos{themeId}");
    PlayerPrefs.Save();
}

 - Se quiser integrar com tela de loading, fade, ou sistema de confirmação visual (em vez de EditorUtility.DisplayDialog), posso adicionar isso também! 🚀
