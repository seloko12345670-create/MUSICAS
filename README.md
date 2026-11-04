# KAMY — YOUR MUSIC. YOUR WORLD.
Player de música 100% offline para Android (Kotlin, Compose, Room, Media3).

## Como gerar o APK
**Opção A — Android Studio (recomendado)**
1. Instale o Android Studio (Koala ou mais novo) e abra a pasta `KAMY`.
2. Aguarde o *Gradle Sync* (precisa de internet só nesse momento, para baixar dependências).
3. `Build > Build Bundle(s) / APK(s) > Build APK(s)`.
4. APK em `app/build/outputs/apk/debug/app-debug.apk` -> copie para o celular e instale.

**Opção B — GitHub Actions (sem instalar nada)**
Suba a pasta para um repositório no GitHub. O workflow `.github/workflows/build.yml`
compila e publica o APK em *Actions > (último run) > Artifacts > KAMY-debug-apk*.

## O que está implementado (Etapas 1–4)
- **Etapa 1:** Room (SQLite) com migrações; `PlaybackService` (Media3 + MediaSession: segundo plano,
  tela desligada, notificação, tela de bloqueio, Bluetooth); `PlaybackTracker` com contagem real.
- **Etapa 2:** biblioteca via MediaStore, importar arquivos, escolher pasta, ler metadados, capas,
  agrupar por artista/álbum/gênero/pasta, detectar duplicatas e arquivos indisponíveis, ordenar.
- **Etapa 3:** playlists (criar, renomear, capa, adicionar/remover, reordenar, duplicar, excluir, aleatório),
  5 playlists automáticas, favoritos, busca instantânea local.
- **Etapa 4:** tela Meu Resumo (filtros, totais, favoritos, TOP 10, gráfico, relatório) com dados reais.
- Player: play/pause, próxima/anterior, progresso, volume, aleatório, repetição, fila, velocidade, favoritar,
  mini player.

## Como a contagem de tempo funciona
`PlaybackTracker` abre um intervalo quando `Player.isPlaying` vira `true` e fecha quando vira `false`
(pausa, buffering, fim, troca). Só intervalos fechados/abertos de áudio tocando somam.
Grava no Room a cada 10 s tocando e em: pausa, troca de faixa, fim e destruição do serviço.
Cada audição tem UUID (chave primária): regravar atualiza a mesma linha, sem duplicar.
Se o sistema matar o processo de repente, a perda máxima é ~10 s.
"Reprodução" = audição com >= 5 s realmente tocados (`MIN_PLAY_MS`). "Música mais ouvida" = maior tempo escutado.

## Checklist de testes (item 14) — execute no celular
1. Abra o app, conceda permissão de áudio/notificações, confirme que as músicas aparecem.
2. Toque uma música por 5 min -> pause 10 min -> toque mais 5 min.
3. Aba Resumo (filtro "Hoje"): deve marcar ~10 min.
4. Feche o app (deslize) e abra: dados mantidos. Force parada: dados mantidos.
5. Reinicie o celular: dados mantidos.
6. Ative modo avião (Wi-Fi e dados off): tudo continua funcionando.
7. Toque, apague a tela e espere: segue tocando; teste notificação, tela de bloqueio e fone Bluetooth.

## Pendente (próximas etapas)
Equalizador, crossfade, temporizador (sleep), letras .lrc, cores dinâmicas pela capa, KAMY Wrapped,
tela de Configurações/DataStore (tema AMOLED, ordenação personalizada), arte na notificação, fila persistida,
fonte customizada.
