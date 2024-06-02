ANU WORLD TABLE TENIS
============



<EMBED> 로고
## 소개
ANUWORLDTABLETENIS 프로젝트는 안동대학교 컴퓨터 그래픽스 수업에서 진행하는 팀 프로젝트 입니다.
해당 프로젝트는 C++과 SDL 라이브러리를 사용하여 만든 2D 탁구 게임 엔진입니다.

## 주요 기능

1. 싱글톤 패턴 적용: GameEngine 클래스는 싱글톤 패턴을 적용하여 게임 엔진의 유일한 인스턴스를 관리합니다. 
2. 게임 초기화: SDL 창, 렌더러, 폰트, 사운드 등의 초기화 및 게임 자산 로드를 수행합니다.
3. 게임 루프: 입력 처리, 게임 로직 업데이트 및 렌더링을 수행하는 게임 루프가 구현되어 있습니다.
4. 입력 처리: 마우스 및 키보드 이벤트를 처리하여 플레이어의 조작을 제어합니다.
5. 게임 로직: 패들 및 공의 이동, 충돌 감지, 점수 계산 등의 게임 로직이 구현되어 있습니다.
6. 렌더링: SDL을 사용하여 게임 객체 및 텍스트를 렌더링합니다.
7. 사운드: SDL_mixer를 사용하여 사운드 이펙트를 재생합니다.
8. 게임 종료 조건: 플레이어나 AI가 일정 점수에 도달할 경우 게임을 종료합니다.
9. 메모리 관리: SDL 리소스의 올바른 해제 및 게임 종료 시 정리 작업이 수행됩니다.
10. 각종 클래스: 게임 객체를 나타내는 Sprite 클래스 등의 여러 클래스 가 구현되어 있습니다.



## 사용 툴
- Visual Studio 2022
- C++
- SDL
- FL STUDIO 20
- Adobe PhotoShop

## 참고 자료

*  [SDL 공식 문서](https://www.libsdl.org/)
*  [SDL_mixer 공식 문서]
*  [SDL_ttf 공식 문서]

----

## 상세내용 및 코드
### GameMenu.cpp

<EMBED>


**해당 cpp는 게임을 처음 시작했을때 나타나는 인트로 화면입니다.**
- FadeIn 함수는 배경, 타이틀, 버튼을 서서히 나타나게 합니다.
- main 함수는 SDL, SDL_mixer, SDL_image, SDL_ttf를 초기화하고, 창과 렌더러를 생성합니다.
- 배경 음악과 효과음을 로드하고 재생합니다.
- 버튼 및 타이틀 이미지를 불러오고, 화면에 표시합니다.
- 마우스 입력을 처리하여 버튼에 호버 효과를 적용하고, 클릭 이벤트를 처리합니다.
- 게임 캐릭터 선택 화면을 표시하고 게임 엔진을 초기화합니다.
- 게임 루프를 실행하여 게임이 종료될 때까지 사용자 입력, 업데이트 및 렌더링을 처리합니다.
- 게임이 종료되면 메모리를 정리하고 SDL 및 믹서를 종료합니다.
  



      #define SDL_MAIN_HANDLED
      
      #include <iostream>
      #include <SDL.h>
      #include <SDL_ttf.h>
      #include <SDL_image.h>
      #include <SDL_mixer.h>
      #include <string>
      #include "GameEngine.h"
      #include "ChooseCharacter.h"
      
      const int BUTTON_WIDTH = 200;
      const int BUTTON_HEIGHT = 100;
      
      const char* BACKGROUND_IMAGE_PATH = "Assets/background.png";
      const char* BUTTON_IMAGE_PATH = "Assets/start_button.png";
      const char* BUTTON_HOVER_IMAGE_PATH = "Assets/start_buttonout.png";
      const char* TITLE_IMAGE_PATH = "Assets/title.png";
      const char* BACKGROUND_MUSIC_PATH = "Assets/background_music.mp3"; // Path to your background music file
      const char* HOVER_SOUND_PATH = "Assets/hover_sound.wav"; // Path to your hover sound effect
      const char* CLICK_SOUND_PATH = "Assets/click_sound.wav"; // Path to your click sound effect
      const char* NEW_SOUND_PATH = "Assets/awtt.mp3"; // Path to your new sound effect
      
      class Button {
      public:
          Button(const char* imagePath, const char* hoverImagePath, int x, int y, int w, int h, SDL_Renderer* renderer)
              : posX(x), posY(y), width(w), height(h), buttonRenderer(renderer),
              defaultTexture(nullptr), hoverTexture(nullptr), isHovered(false) {
              defaultTexture = IMG_LoadTexture(renderer, imagePath);
              hoverTexture = IMG_LoadTexture(renderer, hoverImagePath);
              if (!defaultTexture || !hoverTexture) {
                  std::cerr << "Failed to load button texture." << std::endl;
              }
              SDL_QueryTexture(defaultTexture, nullptr, nullptr, &texW, &texH);
              srcRect = { 0, 0, texW, texH };
              destRect = { posX, posY, width, height };
          }
      
          ~Button() {
              SDL_DestroyTexture(defaultTexture);
              SDL_DestroyTexture(hoverTexture);
          }
      
          void Render(Uint8 alpha) {
              SDL_SetTextureAlphaMod(defaultTexture, alpha);
              SDL_SetTextureAlphaMod(hoverTexture, alpha);
              if (isHovered && hoverTexture) {
                  SDL_RenderCopy(buttonRenderer, hoverTexture, &srcRect, &destRect);
              }
              else if (defaultTexture) {
                  SDL_RenderCopy(buttonRenderer, defaultTexture, &srcRect, &destRect);
              }
              else {
                  std::cerr << "Button texture is null, cannot render." << std::endl;
              }
          }
      
          bool IsHovered(int mouseX, int mouseY) {
              return (mouseX >= posX && mouseX <= posX + width && mouseY >= posY && mouseY <= posY + height);
          }
      
          void SetHovered(bool hovered) {
              isHovered = hovered;
          }
      
      private:
          SDL_Texture* defaultTexture;
          SDL_Texture* hoverTexture;
          SDL_Rect srcRect;
          SDL_Rect destRect;
          int posX, posY, width, height, texW, texH;
          SDL_Renderer* buttonRenderer;
          bool isHovered;
      };
      
      class Background {
      public:
          Background(const char* imagePath, SDL_Renderer* renderer)
              : backgroundTexture(nullptr), backgroundRenderer(renderer) {
              backgroundTexture = IMG_LoadTexture(renderer, imagePath);
              if (!backgroundTexture) {
                  std::cerr << "Failed to load background image." << std::endl;
              }
          }
      
          ~Background() {
              SDL_DestroyTexture(backgroundTexture);
          }
      
          void Render(Uint8 alpha) {
              SDL_SetTextureAlphaMod(backgroundTexture, alpha);
              if (backgroundTexture) {
                  SDL_RenderCopy(backgroundRenderer, backgroundTexture, nullptr, nullptr);
              }
              else {
                  std::cerr << "Background texture is null, cannot render." << std::endl;
              }
          }
      
      private:
          SDL_Texture* backgroundTexture;
          SDL_Renderer* backgroundRenderer;
      };
      
      class Title {
      public:
          Title(const char* imagePath, int x, int y, int w, int h, SDL_Renderer* renderer)
              : posX(x), posY(y), width(w), height(h), titleRenderer(renderer), titleTexture(nullptr) {
              titleTexture = IMG_LoadTexture(renderer, imagePath);
              if (!titleTexture) {
                  std::cerr << "Failed to load title texture." << std::endl;
              }
              SDL_QueryTexture(titleTexture, nullptr, nullptr, &texW, &texH);
              srcRect = { 0, 0, texW, texH };
              destRect = { posX, posY, width, height };
          }
      
          ~Title() {
              SDL_DestroyTexture(titleTexture);
          }
      
          void Render(Uint8 alpha) {
              SDL_SetTextureAlphaMod(titleTexture, alpha);
              if (titleTexture) {
                  SDL_RenderCopy(titleRenderer, titleTexture, &srcRect, &destRect);
              }
              else {
                  std::cerr << "Title texture is null, cannot render." << std::endl;
              }
          }
      
      private:
          SDL_Texture* titleTexture;
          SDL_Rect srcRect;
          SDL_Rect destRect;
          int posX, posY, width, height, texW, texH;
          SDL_Renderer* titleRenderer;
      };
      
      void FadeIn(SDL_Renderer* renderer, Background& background, Title& title, Button& button, Mix_Chunk* newSound, int duration = 3500) {
          Uint32 startTime = SDL_GetTicks();
          Uint32 elapsedTime = 0;
          Uint8 alpha = 0;
      
          // First phase: Fade in background
          while (elapsedTime < duration) {
              elapsedTime = SDL_GetTicks() - startTime;
              alpha = static_cast<Uint8>((elapsedTime * 255) / duration);
              if (alpha > 255) alpha = 255;
      
              SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
              SDL_RenderClear(renderer);
      
              background.Render(alpha);
      
              SDL_RenderPresent(renderer);
              SDL_Delay(16); // To control the frame rate (approx 60 fps)
          }
      
          // Reset start time for the second phase
          startTime = SDL_GetTicks();
          elapsedTime = 0;
          alpha = 0;
      
          // Second phase: Fade in title and button
          while (elapsedTime < duration) {
              elapsedTime = SDL_GetTicks() - startTime;
              alpha = static_cast<Uint8>((elapsedTime * 255) / duration);
              if (alpha > 255) alpha = 255;
      
              SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
              SDL_RenderClear(renderer);
      
              background.Render(255);
              title.Render(alpha);
              button.Render(alpha);
      
              SDL_RenderPresent(renderer);
      
              // Play new sound effect if it exists
              if (newSound) {
                  Mix_PlayChannel(-1, newSound, 0);
                  newSound = nullptr; // Ensure it only plays once
              }
      
              SDL_Delay(16); // To control the frame rate (approx 60 fps)
          }
      }
      
      int main(int argc, char* argv[]) {
          if (SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO) != 0) {
              std::cerr << "SDL initialization failed: " << SDL_GetError() << std::endl;
              return -1;
          }
      
          if (Mix_OpenAudio(44100, MIX_DEFAULT_FORMAT, 2, 2048) < 0) {
              std::cerr << "SDL_mixer initialization failed: " << Mix_GetError() << std::endl;
              return -1;
          }
      
          SDL_Window* window = SDL_CreateWindow("ANU WORLD TABLE TENIS", SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
          SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
      
          if (TTF_Init() != 0) {
              std::cerr << "TTF initialization failed: " << TTF_GetError() << std::endl;
              return -1;
          }
      
          // Load background music
          Mix_Music* backgroundMusic = Mix_LoadMUS(BACKGROUND_MUSIC_PATH);
          if (!backgroundMusic) {
              std::cerr << "Failed to load background music: " << Mix_GetError() << std::endl;
              return -1;
          }
          Mix_PlayMusic(backgroundMusic, -1); // Play music indefinitely
      
          // Load sound effects
          Mix_Chunk* hoverSound = Mix_LoadWAV(HOVER_SOUND_PATH);
          if (!hoverSound) {
              std::cerr << "Failed to load hover sound effect: " << Mix_GetError() << std::endl;
              return -1;
          }
      
          Mix_Chunk* clickSound = Mix_LoadWAV(CLICK_SOUND_PATH);
          if (!clickSound) {
              std::cerr << "Failed to load click sound effect: " << Mix_GetError() << std::endl;
              return -1;
          }
      
          Mix_Chunk* newSound = Mix_LoadWAV(NEW_SOUND_PATH); // Load new sound effect
          if (!newSound) {
              std::cerr << "Failed to load new sound effect: " << Mix_GetError() << std::endl;
              return -1;
          }
      
          Button startButton(BUTTON_IMAGE_PATH, BUTTON_HOVER_IMAGE_PATH, 300, 400, BUTTON_WIDTH, BUTTON_HEIGHT, renderer);
          Background background(BACKGROUND_IMAGE_PATH, renderer);
      
          int titleWidth = 400; // Adjust this as needed
          int titleHeight = 300; // Adjust this as needed
          int titlePosX = (800 - titleWidth) / 2; // Centered horizontally
          int titlePosY = 50; // Position it near the top
      
          Title title(TITLE_IMAGE_PATH, titlePosX, titlePosY, titleWidth, titleHeight, renderer);
      
          bool isRunning = true;
          bool isHoverSoundPlayed = false;
          SDL_Event event;
      
          // Perform fade in
          FadeIn(renderer, background, title, startButton, newSound);
      
          while (isRunning) {
              while (SDL_PollEvent(&event)) {
                  switch (event.type) {
                  case SDL_QUIT:
                      isRunning = false;
                      break;
                  case SDL_MOUSEMOTION: {
                      int mouseX, mouseY;
                      SDL_GetMouseState(&mouseX, &mouseY);
                      bool isHovered = startButton.IsHovered(mouseX, mouseY);
                      startButton.SetHovered(isHovered);
      
                      if (isHovered && !isHoverSoundPlayed) {
                          Mix_PlayChannel(-1, hoverSound, 0);
                          isHoverSoundPlayed = true;
                      }
                      else if (!isHovered) {
                          isHoverSoundPlayed = false;
                      }
                      break;
                  }
                  case SDL_MOUSEBUTTONDOWN: {
                      int mouseX, mouseY;
                      SDL_GetMouseState(&mouseX, &mouseY);
                      if (startButton.IsHovered(mouseX, mouseY)) {
                          Mix_PlayChannel(-1, clickSound, 0);
                          SDL_Delay(500); // Wait for the click sound to finish before moving on
      
                          if (!ShowCharacterSelection(window, renderer)) {
                              std::cerr << "Failed to show character selection screen." << std::endl;
                              return -1;
                          }
                          GameEngine* game = GameEngine::Instance();
                          if (!game->InitGameEngine()) {
                              std::cerr << "Failed to initialize game engine." << std::endl;
                              return -1;
                          }
                          game->InitGameWorld();
                          while (game->IsRunning()) {
                              game->Input();
                              game->Update();
                              game->Render();
                          }
                          game->Quit();
                          isRunning = false;
                      }
                      break;
                  }
                  }
              }
      
              SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
              SDL_RenderClear(renderer);
              background.Render(255);
              title.Render(255);
              startButton.Render(255);
              SDL_RenderPresent(renderer);
          }
      
          // Clean up sound effects
          Mix_FreeChunk(hoverSound);
          Mix_FreeChunk(clickSound);
          Mix_FreeChunk(newSound); // Free new sound effect resource
          // Clean up music resource
          Mix_FreeMusic(backgroundMusic);
      
          SDL_DestroyRenderer(renderer);
          SDL_DestroyWindow(window);
          Mix_CloseAudio();
          TTF_Quit();
          SDL_Quit();
        
          return 0;
      }


---- 
### Character Select.cpp

<EMBED>


**해당 cpp는 캐릭터를 선택하는 화면입니다..**

- 5개의 Charater를 선택할수 있고 각각의 캐릭터 객체에 마우스를 올릴시 강조하는 hover효과가 적용되어 있습니다.
- Character 클래스는 각각의 캐릭터를 나타내며, 원본 이미지와 변경된 이미지 경로를 저장하고 이미지를 렌더링합니다. 또한, 이미지를 변경하는 메서드와 클릭 여부를 확인하는 메서드를 제공합니다.
- RenderText 함수는 주어진 텍스트를 렌더링하여 텍스처를 반환합니다.
- FadeInCharacter 함수는 캐릭터가 서서히 나타나는 페이드 인 효과를 구현합니다. 해당 함수를 이용하여 극적인 연출을 진행하였습니다.
- ShowCharacterSelection 함수는 캐릭터 선택 화면을 표시합니다. 각 캐릭터를 클릭하면 해당 캐릭터가 선택되고, 선택된 캐릭터에 대한 정보가 표시됩니다. 또한, 배경 음악과 효과음을 재생하고, 효과음을 특정 이벤트에 따라 재생합니다.
- main 함수에서는 SDL을 초기화하고 윈도우 및 렌더러를 생성한 후 캐릭터 선택 화면을 표시합니다.





        #include <SDL.h>
        #include <SDL_image.h>
        #include <SDL_ttf.h>
        #include <SDL_mixer.h>
        #include <iostream>
        
        // 캐릭터 클래스 정의
        class Character {
        public:
            Character(const char* imagePath, const char* outImagePath, int x, int y, int w, int h, SDL_Renderer* renderer)
                : imagePath(imagePath), outImagePath(outImagePath), posX(x), posY(y), width(w), height(h), characterRenderer(renderer) {
                characterTexture = IMG_LoadTexture(renderer, imagePath);
                if (!characterTexture) {
                    std::cerr << "Failed to load texture: " << IMG_GetError() << std::endl;
                }
                SDL_QueryTexture(characterTexture, nullptr, nullptr, &texW, &texH);
                srcRect = { 0, 0, texW, texH };
                destRect = { posX, posY, width, height };
            }
        
            // 이미지 변경 함수
            void ChangeImage(const char* newImagePath) {
                SDL_DestroyTexture(characterTexture);
                characterTexture = IMG_LoadTexture(characterRenderer, newImagePath);
                if (!characterTexture) {
                    std::cerr << "Failed to load texture: " << IMG_GetError() << std::endl;
                }
            }
        
            // 나머지 캐릭터 이미지로 변경하는 함수
            void ChangeToOutImage() {
                ChangeImage(outImagePath);
            }
        
            // 기본 캐릭터 이미지로 변경하는 함수
            void ChangeToOriginalImage() {
                ChangeImage(imagePath);
            }
        
            void Render(Uint8 alpha = 255) {
                SDL_SetTextureAlphaMod(characterTexture, alpha);
                if (characterTexture) {
                    SDL_RenderCopy(characterRenderer, characterTexture, &srcRect, &destRect);
                }
                else {
                    std::cerr << "Texture is null, cannot render." << std::endl;
                }
            }
        
            bool IsClicked(int mouseX, int mouseY) {
                return (mouseX >= posX && mouseX <= posX + width && mouseY >= posY && mouseY <= posY + height);
            }
        
        private:
            const char* imagePath;  // 원본 이미지 경로
            const char* outImagePath;  // 변경된 이미지 경로
            SDL_Texture* characterTexture;  // 캐릭터 텍스처
            SDL_Rect srcRect;  // 원본 텍스처 영역
            SDL_Rect destRect;  // 대상 렌더링 영역
            int posX, posY, width, height, texW, texH;  // 위치, 크기 변수
            SDL_Renderer* characterRenderer;  // 렌더러 포인터
        };
        
        // 텍스트 렌더링 함수
        SDL_Texture* RenderText(const std::string& message, const std::string& fontFile, SDL_Color color, int fontSize, SDL_Renderer* renderer) {
            TTF_Font* font = TTF_OpenFont(fontFile.c_str(), fontSize);
            if (!font) {
                std::cerr << "Failed to load font: " << TTF_GetError() << std::endl;
                return nullptr;
            }
        
            SDL_Surface* surface = TTF_RenderUTF8_Blended(font, message.c_str(), color);
            if (!surface) {
                std::cerr << "Failed to create surface: " << TTF_GetError() << std::endl;
                TTF_CloseFont(font);
                return nullptr;
            }
        
            SDL_Texture* texture = SDL_CreateTextureFromSurface(renderer, surface);
            if (!texture) {
                std::cerr << "Failed to create texture: " << SDL_GetError() << std::endl;
            }
        
            SDL_FreeSurface(surface);
            TTF_CloseFont(font);
            return texture;
        }
        
        void FadeInCharacter(SDL_Renderer* renderer, Character& character, Mix_Chunk* effectSound, int duration = 1000) {
            Uint32 startTime = SDL_GetTicks();
            Uint32 elapsedTime = 0;
            Uint8 alpha = 0;
        
            while (elapsedTime < duration) {
                elapsedTime = SDL_GetTicks() - startTime;
                alpha = static_cast<Uint8>((elapsedTime * 255) / duration);
                if (alpha > 255) alpha = 255;
        
                SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
                SDL_RenderClear(renderer);
                character.Render(alpha);
        
                SDL_RenderPresent(renderer);
                SDL_Delay(16); // To control the frame rate (approx 60 fps)
            }
        
            // 효과음 재생
            Mix_PlayChannel(-1, effectSound, 0);
        }
        
        bool ShowCharacterSelection(SDL_Window* window, SDL_Renderer* renderer) {
            // TTF 초기화
            if (TTF_Init() == -1) {
                std::cerr << "TTF_Init: " << TTF_GetError() << std::endl;
                return false;
            }
        
            // SDL_mixer 초기화
            if (Mix_OpenAudio(44100, MIX_DEFAULT_FORMAT, 2, 2048) == -1) {
                std::cerr << "Mix_OpenAudio: " << Mix_GetError() << std::endl;
                return false;
            }
        
            // 배경 음악 로드
            Mix_Music* backgroundMusic = Mix_LoadMUS("Assets/background_music1.mp3");
            if (!backgroundMusic) {
                std::cerr << "Failed to load background music: " << Mix_GetError() << std::endl;
                return false;
            }
        
            // 배경 음악 재생
            if (Mix_PlayMusic(backgroundMusic, -1) == -1) {
                std::cerr << "Mix_PlayMusic: " << Mix_GetError() << std::endl;
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
        
            // 효과음 로드
            Mix_Chunk* hoverSound = Mix_LoadWAV("Assets/hover_sound.wav");
            if (!hoverSound) {
                std::cerr << "Failed to load hover sound: " << Mix_GetError() << std::endl;
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
        
            // 페이드인 효과음 로드
            Mix_Chunk* fadeInSound = Mix_LoadWAV("Assets/boom.mp3");
            if (!fadeInSound) {
                std::cerr << "Failed to load fade-in sound: " << Mix_GetError() << std::endl;
                Mix_FreeChunk(hoverSound);
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
            // 캐릭터 선택시 효과음 로드
            Mix_Chunk* selectSound = Mix_LoadWAV("Assets/select.mp3");
            if (!selectSound) {
                std::cerr << "Failed to load click sound: " << Mix_GetError() << std::endl;
                Mix_FreeChunk(hoverSound);
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
            // 김하준 효과음
            Mix_Chunk* khj = Mix_LoadWAV("Assets/announcement/a1.mp3");
            if (!khj) {
                std::cerr << "Failed to load click sound: " << Mix_GetError() << std::endl;
                Mix_FreeChunk(hoverSound);
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
            // 신현진 효과음
            Mix_Chunk* shj = Mix_LoadWAV("Assets/announcement/a2.mp3");
            if (!shj) {
                std::cerr << "Failed to load click sound: " << Mix_GetError() << std::endl;
                Mix_FreeChunk(hoverSound);
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
            // 김종준 효과음
            Mix_Chunk* kjj = Mix_LoadWAV("Assets/announcement/a3.mp3");
            if (!kjj) {
                std::cerr << "Failed to load click sound: " << Mix_GetError() << std::endl;
                Mix_FreeChunk(hoverSound);
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
            // 안현수 효과음
            Mix_Chunk* ahs = Mix_LoadWAV("Assets/announcement/a4.mp3");
            if (!ahs) {
                std::cerr << "Failed to load click sound: " << Mix_GetError() << std::endl;
                Mix_FreeChunk(hoverSound);
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
            // 홍재영 효과음
            Mix_Chunk* hjy = Mix_LoadWAV("Assets/announcement/a5.mp3");
            if (!hjy) {
                std::cerr << "Failed to load click sound: " << Mix_GetError() << std::endl;
                Mix_FreeChunk(hoverSound);
                Mix_FreeMusic(backgroundMusic);
                return false;
            }
        
        
            // 캐릭터 객체 생성
            Character character1("Assets/character1.png", "Assets/character1out.png", 0, 0, 160, 600, renderer);
            Character character2("Assets/character2.png", "Assets/character2out.png", 160, 0, 160, 600, renderer);
            Character character3("Assets/character3.png", "Assets/character3out.png", 320, 0, 160, 600, renderer);
            Character character4("Assets/character4.png", "Assets/character4out.png", 480, 0, 160, 600, renderer);
            Character character5("Assets/character5.png", "Assets/character5out.png", 640, 0, 160, 600, renderer);
        
            bool isRunning = true;
            SDL_Event event;
            std::string selectedCharacter = "";
            SDL_Texture* messageTexture = nullptr;
            SDL_Color whiteColor = { 255, 255, 255, 255 };
        
            Character* lastHoveredCharacter = nullptr;
        
            // Perform fade in for each character
            FadeInCharacter(renderer, character1, fadeInSound);
            FadeInCharacter(renderer, character2, fadeInSound);
            FadeInCharacter(renderer, character3, fadeInSound);
            FadeInCharacter(renderer, character4, fadeInSound);
            FadeInCharacter(renderer, character5, fadeInSound);
        
            while (isRunning) {
                while (SDL_PollEvent(&event)) {
                    if (event.type == SDL_QUIT) {
                        isRunning = false;
                    }
                    else if (event.type == SDL_MOUSEMOTION) {
                        int mouseX, mouseY;
                        SDL_GetMouseState(&mouseX, &mouseY);
                        Character* hoveredCharacter = nullptr;
        
                        if (character1.IsClicked(mouseX, mouseY)) {
                            hoveredCharacter = &character1;
                            character1.ChangeToOriginalImage();
                            character2.ChangeToOutImage();
                            character3.ChangeToOutImage();
                            character4.ChangeToOutImage();
                            character5.ChangeToOutImage();
                        }
                        else if (character2.IsClicked(mouseX, mouseY)) {
                            hoveredCharacter = &character2;
                            character1.ChangeToOutImage();
                            character2.ChangeToOriginalImage();
                            character3.ChangeToOutImage();
                            character4.ChangeToOutImage();
                            character5.ChangeToOutImage();
                        }
                        else if (character3.IsClicked(mouseX, mouseY)) {
                            hoveredCharacter = &character3;
                            character1.ChangeToOutImage();
                            character2.ChangeToOutImage();
                            character3.ChangeToOriginalImage();
                            character4.ChangeToOutImage();
                            character5.ChangeToOutImage();
                        }
                        else if (character4.IsClicked(mouseX, mouseY)) {
                            hoveredCharacter = &character4;
                            character1.ChangeToOutImage();
                            character2.ChangeToOutImage();
                            character3.ChangeToOutImage();
                            character4.ChangeToOriginalImage();
                            character5.ChangeToOutImage();
                        }
                        else if (character5.IsClicked(mouseX, mouseY)) {
                            hoveredCharacter = &character5;
                            character1.ChangeToOutImage();
                            character2.ChangeToOutImage();
                            character3.ChangeToOutImage();
                            character4.ChangeToOutImage();
                            character5.ChangeToOriginalImage();
                        }
        
                        if (hoveredCharacter && hoveredCharacter != lastHoveredCharacter) {
                            Mix_PlayChannel(-1, hoverSound, 0);
                            lastHoveredCharacter = hoveredCharacter;
                        }
                    }
                    else if (event.type == SDL_MOUSEBUTTONDOWN) {
                        int mouseX, mouseY;
                        SDL_GetMouseState(&mouseX, &mouseY);
                        if (character1.IsClicked(mouseX, mouseY)) {
                            selectedCharacter = "You select KIM HA JOON";
                            Mix_PlayChannel(-1, selectSound , 0);
                            Mix_PlayChannel(-1, khj , 0);
                        }
                        else if (character2.IsClicked(mouseX, mouseY)) {
                            selectedCharacter = "You select SHIN HYUN JIN";
                            Mix_PlayChannel(-1, selectSound , 0);
                            Mix_PlayChannel(-1, shj, 0);
                        }
                        else if (character3.IsClicked(mouseX, mouseY)) {
                            selectedCharacter = "You select KIM JONG JUN";
                            Mix_PlayChannel(-1, selectSound , 0);
                            Mix_PlayChannel(-1, kjj, 0);
                        }
                        else if (character4.IsClicked(mouseX, mouseY)) {
                            selectedCharacter = "You select AHN HYUN SOO";
                            Mix_PlayChannel(-1, selectSound , 0);
                            Mix_PlayChannel(-1, ahs, 0);
                        }
                        else if (character5.IsClicked(mouseX, mouseY)) {
                            selectedCharacter = "You select HONG JAE YOUNG";
                            Mix_PlayChannel(-1, selectSound , 0);
                            Mix_PlayChannel(-1, hjy, 0);
                        }
                        if (!selectedCharacter.empty()) {
                            messageTexture = RenderText(selectedCharacter, "Assets/Fonts/LTYPE.TTF", whiteColor, 24, renderer);
                            SDL_RenderClear(renderer);
                            character1.Render();
                            character2.Render();
                            character3.Render();
                            character4.Render();
                            character5.Render();
        
                            if (messageTexture) {
                                int texW = 0;
                                int texH = 0;
                                SDL_QueryTexture(messageTexture, nullptr, nullptr, &texW, &texH);
                                SDL_Rect dstRect = { 50, 50, texW, texH };
                                SDL_RenderCopy(renderer, messageTexture, nullptr, &dstRect);
                            }
        
                            SDL_RenderPresent(renderer);
                            SDL_Delay(4500);  // 4.5초 대기
                            isRunning = false;
                        }
                    }
                }
        
                if (isRunning) {
                    SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
                    SDL_RenderClear(renderer);
        
                    character1.Render();
                    character2.Render();
                    character3.Render();
                    character4.Render();
                    character5.Render();
        
                    SDL_RenderPresent(renderer);
                }
            }
        
            if (messageTexture) {
                SDL_DestroyTexture(messageTexture);
            }
        
            Mix_FreeChunk(hoverSound);
            Mix_FreeChunk(fadeInSound);
            Mix_FreeMusic(backgroundMusic);
            Mix_CloseAudio();
            TTF_Quit();
        
            // 선택창이 종료되면 윈도우를 닫음
            SDL_DestroyWindow(window);
        
            return true;
        }
        
        int main() {
            // SDL 초기화
            if (SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO) != 0) {
                std::cerr << "SDL_Init Error: " << SDL_GetError() << std::endl;
                return 1;
            }
        
            // 윈도우 생성
            SDL_Window* window = SDL_CreateWindow("Character Selection", SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, SDL_WINDOW_SHOWN);
            if (!window) {
                std::cerr << "SDL_CreateWindow Error: " << SDL_GetError() << std::endl;
                SDL_Quit();
                return 1;
            }
        
            // 렌더러 생성
            SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
            if (!renderer) {
                std::cerr << "SDL_CreateRenderer Error: " << SDL_GetError() << std::endl;
                SDL_DestroyWindow(window);
                SDL_Quit();
                return 1;
            }
        
            // 캐릭터 선택 화면 표시
            ShowCharacterSelection(window, renderer);
        
            // SDL 종료
            SDL_DestroyRenderer(renderer);
            SDL_Quit();
            return 0;
        }




----
### Game Engine.cpp

<EMBED>


**해당 cpp는 메인 탁구 게임 입니다.**

**1. 싱글톤 GameEngine 클래스** 
- GameEngine 클래스는 Singleton 패턴을 구현하여 프로그램 전체에서하나의 게임 엔진 인스턴스만 존재하도록 보장합니다.


**2. 초기화 및 설정**
- SDL 창, 렌더러, 폰트 및 오디오용 SDL_mixer의 초기화.
- 스프라이트 및 사운드와 같은 게임 자산의 로드.
- 공 및 패들과 같은 스프라이트의 위치를 포함한 초기 게임 월드 설정.

**3. 게임 루프**
- main 함수는 게임 엔진을 설정하고, 게임을 시작하기 위해 마우스 클릭을 대기하고, 게임 루프에 진입합니다.
- 게임 루프 내에서는 입력을 계속 처리하고, 게임 상태를 업데이트하고, 장면을 렌더링합니다.

**4. 입력 처리**
- 플레이어 패들을 제어하기 위해 마우스 이동 및 버튼 이벤트를 처리합니다.
- GameEngine 내의 Input 메서드는 마우스 이동 및 게임 종료와 같은 SDL 이벤트를 처리합니다.

**5. 게임 로직**
- PaddleHumanMoveMouse, UpdateBallPosition, ReverseBallPositionY, PaddleAIMove 등의 함수는 패들 이동, 공 물리학 및 충돌 감지와 같은 게임 메커니즘을 처리합니다.
- CheckCollision, NotAIArea, InAIArea와 같은 메서드는 충돌 감지와 게임 로직에 사용됩니다.

**6. 렌더링**
- SDL 렌더링 함수를 사용하여 게임 요소를 렌더링합니다.
- SDL_ttf를 사용하여 점수 및 승리 메시지를 표시하는 텍스트 렌더링.
  
**7. 오디오**
- SDL_mixer를 사용하여 사운드 효과를 로드하고 재생합니다.
  
**8. 게임 종료 조건**
- 플레이어가 다섯 점을 획득하면 게임이 종료됩니다.




        #pragma once
        #include <iostream>
        #include <string>
        #include <algorithm>
        #include <SDL.h>
        #include <SDL_ttf.h> // to render ttf fonts
        #include <SDL_image.h>
        #include "SDL_mixer.h"
        
        using namespace std;
        
        #define WINDOW_WIDTH 800
        #define WINDOW_HEIGHT 600
        
        class Sprite
        {
        
        private:
            SDL_Rect spriteSrcRect; // defines part of spreadsheet we'd like to render
            SDL_Surface* sprite;    // the handle to the image
            SDL_Texture* texture;   // texture holds sprite as a texture after loading the above
        
        
        public:
            SDL_Rect spriteDestRect; // position we'd like to render the above
            Sprite(const char* spritePath, SDL_Rect srcRect, SDL_Rect destRect, SDL_Renderer* renderer);
            ~Sprite();
            void Render(SDL_Renderer* renderer);
        };
        
        class GameEngine
        {
        private:
        
            Mix_Chunk* hitSound; // 탁구채와 공이 충돌할 때 재생할 소리
            Mix_Chunk* chantSound;// 점수 획득시 관중 사운드
            SDL_Event event;        // to handle events
            int mouseX, mouseY;     // for mouse coordinates
            SDL_Window* window;     // moved from InitGameEngine
            SDL_Renderer* renderer;
            bool isRunning = true;
            TTF_Font* font;             // font declarations
            SDL_Surface* fontSurface;
            SDL_Texture* fontTexture;
            SDL_Rect textRectScore;     // defines 'score' text on screen
            void RenderFont(const char* text, int x, int y, bool isRefreshText); // font related function
            int p1score = 0;           // for demo score is hardcoded, this score will be tracked and made in a different function
            int aiscore = 0;           // AI paddle score
            string s1 = to_string(p1score);
            string s2 = to_string(aiscore);
            string aiwins = ".";
            string p1wins = ".";
            Sprite* background;         // Sprite Actors
            Sprite* paddleHuman;
            Sprite* paddleAI;
            Sprite* ball;
            void InitializeSpriteBackground(const char* loadPath, int cellX, int cellY, int cellWidth, int cellHeight,
                int destX, int destY, int destW, int destH);
            void InitializeSpritepaddleHuman(const char* loadPath, int cellX, int cellY, int cellWidth, int cellHeight,
                int destX, int destY, int destW, int destH);
            void InitializeSpritepaddleAI(const char* loadPath, int cellX, int cellY, int cellWidth, int cellHeight,
                int destX, int destY, int destW, int destH);
            void InitializeSpriteBall(const char* loadPath, int cellX, int cellY, int cellWidth, int cellHeight,
                int destX, int destY, int destW, int destH);
            SDL_Rect divider;           // Visual Improvements: center divider
            SDL_Rect p1goal;
            SDL_Rect aigoal;
            SDL_Rect hide1;
            SDL_Rect hide2;
        
        
        public:
            int speed_x, speed_y;      // x and y speeds of the ball
            int direction[2] = { -1, 1 }; // x and y array directions
            static GameEngine* Instance(); // function returns
            bool InitGameEngine();
            bool CheckCollision(SDL_Rect A, SDL_Rect B);
            void PaddleHumanMoveMouse(); // Sprite, to:
            void UpdateBallPosition();
            void ReverseBallPositionY();
            void ResetBallPositionX();
            void PaddleAIMove();
            void ResetPaddleAIBallNotAIArea();
            void BallInAIArea();
            void CheckBallPaddleCollision();
            void BallInPaddleHumanGoalArea();
            void BallInPaddleAIGoalArea(); // Sprite, from
            bool NotAIArea(SDL_Rect BALL, SDL_Rect AIAREA);
            bool InAIArea(SDL_Rect BALL, SDL_Rect AIAREA);
            void PlayerServe();
            void AIServe();
            void AI();
            void AddToPlayerScore();
            void AddToAIScore();
            void KeepPlayerScore();
            void KeepAIScore();
            void PlayerWins();
            void AIWins();
            void InitGameWorld();
            void Render();
            void Input();
            void Quit();
            void Update();
            bool IsRunning() { return isRunning; }
            bool IsMouseClick();
        };
