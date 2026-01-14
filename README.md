# SSD1306 Display Tool

A command-line tool for displaying text and images on SSD1306 OLED displays via I²C interface. This was written for driving a display connecting to the GPIO pins of a Raspberry Pi, but should work on any Linux hardware with an I2C bus.

The tool supports a web-based display mode for testing things out without an actual SSD1306 display.

[SSD1306]: https://cdn-shop.adafruit.com/datasheets/SSD1306.pdf

## Requirements

- Go 1.24.0 or later
- SSD1306 OLED display connected via I²C

## Building

```bash
make
```

## Usage

```bash
Usage of ././display1306:
  -k, --clear                     clear the display
  -d, --device string             path to i2c device (default "/dev/i2c-1")
  -n, --dry-run                   run without actual hardware
      --duration duration         maximum duration to run loop (0 for unlimited)
  -f, --font string               path to truetype font file
  -s, --font-size float           font size in points (ignored if --font not provided) (default 13)
  -i, --image                     interpret non-option arguments as image filenames
      --image-interval duration   interval between images (default 30ms)
  -l, --line uint                 line number to start printing (1-based) (default 1)
      --loop                      loop through images continuously
      --wait                      pause and require ctrl-c to exit
```

## Displaying text

Display simple text:

```bash
./display1306 "Hello, World!"
```

Display text on a specific line:

```bash
./display1306 --line 3 "This is line 3"
```

Read from stdin:

```bash
echo -e "Line 1\nLine 2\nLine 3" | ./display1306
```

## Displaying images

Your images should be 128x64 black-and-white png images.

Display a single image:

```bash
./display1306 --image image1.png
```

Display a looping series of images, 1 per second:

```bash
./display1306 --image --loop --image-interval 1s image1.png image2.png image3.png
```

When specifying the list of image filenames, you can insert `@`-prefixed commands to control the behavior of the display:

- `@clear` -- clear the display
- `@pause=<duration>` -- pause for the indicated interval. This is a Go-style time duration, so `2s` for two seconds, `100ms` for 100 milliseconds, etc.
- `@interval=<duration>` -- change the image interval from this point forward

The following will:

1. Display `image1.png`
2. Clear the screen (and pause for 1 second)
3. Display `image2.png`
4. Pause for 5 seconds
5. Display `image3.png`
6. Loop

```bash
./display1306 --image --loop --image-interval 1s image1.png @clear image2.png @pause=5s image3.png
```
