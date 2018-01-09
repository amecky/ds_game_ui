# ds_game_ui

Simple single header C++ game ui implementation used inside my games. It is based on the IMGUI ideas and supports
a simple immediate mode game user interface.
It requires the SpriteBatchBuffer and of course diesel

## Include the header

```
#define DS_GAME_UI_IMPLEMENTATION
#include <ds_game_ui.h>
```

## Prepare the game ui

```
static const ds::vec2 FONT_DEF[] = {
	ds::vec2(1,24),   // A
	ds::vec2(24,21),  // B
	........
};

void prepareFontInfo(dialog::FontInfo* info) {
	// default for every character just empty space
	for (int i = 0; i < 255; ++i) {
		info->texture_rects[i] = ds::vec4(0, 100, 20, 19);
	}
	// numbers
	for (int c = 48; c <= 57; ++c) {
		int idx = (int)c - 48;
		info->texture_rects[c] = ds::vec4(548 + idx * 22, 780, 22, 19);
	}

	for (int c = 65; c <= 90; ++ c) {
		ds::vec2 fd = FONT_DEF[(int)c - 65];
		info->texture_rects[c] = ds::vec4(0.0f + fd.x, 780.0f, fd.y, 19.0f);
	}
}
```

```
dialog::FontInfo fontInfo;
prepareFontInfo(&fontInfo);
dialog::init(_spriteBuffer, fontInfo);
```

## Example using float in animation

```
enum FloatInDirection {
	FID_LEFT,
	FID_RIGHT
};

static int floatButton(float time, float ttl, FloatInDirection dir) {
	if (time <= ttl) {
		if (dir == FloatInDirection::FID_LEFT) {
			return tweening::interpolate(tweening::easeOutElastic, -200, 512, time, ttl);
		}
		else {
			return tweening::interpolate(tweening::easeOutElastic, 1020, 512, time, ttl);
		}
	}
	return 512;
}

const static int LOGO_Y_POS = 600;

int showMainMenu(float time, float ttl) {
	int ret = 0;
	dialog::begin();
	int dy = LOGO_Y_POS;
	if (time <= ttl) {
		dy = tweening::interpolate(tweening::easeOutElastic, 1000, LOGO_Y_POS, time, ttl);
	}
	dialog::Image(ds::vec2(512, dy), ds::vec4(225, 5, 770, 55));
	dialog::Image(ds::vec2(512, 35), ds::vec4(0, 955, 530, 12));
	int dx = floatButton(time, ttl, FloatInDirection::FID_LEFT);
	if (dialog::Button(ds::vec2(dx, 420), ds::vec4(0, 70, 260, 60))) {
		ret = 1;
	}
	dx = floatButton(time, ttl, FloatInDirection::FID_RIGHT);
	if (dialog::Button(ds::vec2(dx, 290), ds::vec4(270, 70, 260, 60))) {
		ret = 3;
	}
	dx = floatButton(time, ttl, FloatInDirection::FID_LEFT);
	if (dialog::Button(ds::vec2(dx, 160), ds::vec4(270, 130, 260, 60))) {
		ret = 2;
	}
	dialog::end();
	return ret;
}
```