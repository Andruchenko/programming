# programming
#include "calculator.h"

using namespace std;

int Calculator::init()
{
	if (SDL_Init(SDL_INIT_EVERYTHING) != 0)
	{
		cout << "Problem with init: " << SDL_GetError() << endl;
		return 1;
	}
	if (SDL_CreateWindowAndRenderer(SCREEN_WIDTH, SCREEN_HEIGHT, 0, &window, &renderer) != 0)
	{
		cout << "Problem with creating window and renderer: " << SDL_GetError() << endl;
		return 1;
	}
	SDL_SetWindowPosition(window, WIDTH / 2 - SCREEN_WIDTH / 2, HEIGHT / 2 - SCREEN_HEIGHT / 2 );

	letters['0'] = {
		Line{.3, .5, .5, .1},
		Line{.3, .5, .5, .9},
		Line{.7, .5, .5, .1},
		Line{.7, .5, .5, .9},
	};
	letters['1'] = {
		Line{.5, .9, .5, .1},
	};
	letters['2'] = {
		Line{.1, .9, .1, .1},
		Line{.2, .9, .2, .1},
	};
	letters['3'] = {
		Line{.1, .9, .1, .1},
		Line{.2, .9, .2, .1},
		Line{.3, .9, .3, .1},
	};
	letters['4'] = {
		Line{.1, .9, .1, .1},
		Line{.2, .9, .2, .1},
		Line{.3, .9, .3, .1},
		Line{.4, .9, .4, .1},

	};
	letters['5'] = {
		Line{.1, .9, .1, .1},
		Line{.2, .9, .2, .1},
		Line{.3, .9, .3, .1},
		Line{.4, .9, .4, .1},
		Line{.5, .9, .5, .1},

	};
	letters['6'] = {
		Line{.1, .9, .1, .1},
		Line{.2, .9, .2, .1},
		Line{.3, .9, .3, .1},
		Line{.4, .9, .4, .1},
		Line{.5, .9, .5, .1},
		Line{.6, .9, .6, .1},

	};
	letters['7'] = {
		Line{.1, .9, .1, .1},
		Line{.2, .9, .2, .1},
		Line{.3, .9, .3, .1},
		Line{.4, .9, .4, .1},
		Line{.5, .9, .5, .1},
		Line{.6, .9, .6, .1},
		Line{.7, .9, .7, .1},

	};
	letters['8'] = {
		Line{.1, .9, .1, .1},
		Line{.2, .9, .2, .1},
		Line{.3, .9, .3, .1},
		Line{.4, .9, .4, .1},
		Line{.5, .9, .5, .1},
		Line{.6, .9, .6, .1},
		Line{.7, .9, .7, .1},
		Line{.8, .9, .8, .1},

	};
	letters['9'] = {
		Line{.1, .9, .1, .1},
		Line{.2, .9, .2, .1},
		Line{.3, .9, .3, .1},
		Line{.4, .9, .4, .1},
		Line{.5, .9, .5, .1},
		Line{.6, .9, .6, .1},
		Line{.7, .9, .7, .1},
		Line{.8, .9, .8, .1},
		Line{.9, .9, .9, .1},

	};
	letters['-'] = {
		Line{.1, .5, .9, .5},

	};
	letters['+'] = {
		Line{.1, .5, .9, .5},
		Line{.5, .9, .5, .1},

	};
	letters['='] = {
		Line{.1, .7, .9, .7},
		Line{.1, .3, .9, .3},

	};
	letters['*'] = {
		Line{.2, .2, .8, .8},
		Line{.2, .8, .8, .2},

	};
	letters['.'] = {
		Line{.2, .8, .3, .8},

	};
	letters['|'] = {
		Line{.2, .5, .8, .8},
		Line{.2, .5, .8, .2},

	};
	return 0;
}

int Calculator::close()
{
	SDL_DestroyRenderer(renderer);
	SDL_DestroyWindow(window);
	renderer = NULL;
	window = NULL;
	SDL_Quit();
	return 0;
}

void Calculator::exec()
{
	int selected_x, selected_y;
	bool quit = false;
	int number_screen = 0;
	SDL_Event event;
	while (!quit)
	{
		while (SDL_PollEvent(&event))
		{
			//selected_x = (event.button.x ? event.button.x * 4 / SCREEN_WIDTH : -1);
			selected_x = event.button.x * 4 / SCREEN_WIDTH;
			selected_y = event.button.y * 5 / SCREEN_HEIGHT - 1;
			cout << selected_x << " " << selected_y << endl;
			switch (event.type)
			{
			case SDL_QUIT:
				quit = true;
				break;
			case SDL_KEYDOWN:
				switch (event.key.keysym.sym)
				{
				case SDLK_ESCAPE:
					quit = true;
					break;
				}
				break;
			case SDL_MOUSEBUTTONDOWN:

				if (selected_y == -1) break;
				char ch = grid[selected_y][selected_x];
				if ('0' <= ch && ch <= '9')
				{
					number_screen += ch;
					break;
				}
				switch (ch)
				{
				case '|':
					//number_screen.pop_back();
					break;
				case '+':
					break;
				}
				break;
			}
		}
		draw();
	}
}

void Calculator::draw() {
	SDL_SetRenderDrawColor(renderer, 250, 250, 250, 255);
	SDL_RenderClear(renderer);

	for (int i = 0; i < 4; i++)
		for (int j = 0; j < 4; j++)
			drawButton(
				grid[i][j],
				SCREEN_WIDTH / 4 * j,
				SCREEN_HEIGHT / 5 * (i + 1),
				SCREEN_WIDTH / 4,
				SCREEN_HEIGHT / 5);

	//drawScreen();

	SDL_RenderPresent(renderer);
}

void Calculator::drawButton(char ch, int x, int y, int w, int h) {
	int border = 2;
	SDL_Rect outlineRect = { x + border, y + border, w - 2 * border, h - 2 * border };
	SDL_SetRenderDrawColor(renderer, 176, 176, 176, 255);
	SDL_RenderFillRect(renderer, &outlineRect);

	//symbol
	SDL_SetRenderDrawColor(renderer, 255, 100, 100, 255);
	for (Line line : letters[ch])
		SDL_RenderDrawLine(renderer, line[0] * w + x, line[1] * h + y, line[2] * w + x, line[3] * h + y);
}

Calculator::Calculator()
{
	init();
}

Calculator::~Calculator()
{
	close();
}

