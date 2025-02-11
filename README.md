
import argparse
from os import path

from PIL import Image, ImageDraw, ImageFilter

argparser = argparse.ArgumentParser()

argparser.add_argument('input_file', help='File to add faded edges to')
argparser.add_argument('fade_size', type=int, nargs='?', help='Amount of pixels to blur', default=20)
argparser.add_argument('-t', '--top', action='store_true', help='Fade the top edge')
argparser.add_argument('-r', '--right', action='store_true', help='Fade the right edge')
argparser.add_argument('-b', '--bottom', action='store_true', help='Fade the bottom edge')
argparser.add_argument('-l', '--left', action='store_true', help='Fade the left edge')
argparser.add_argument('-f', '--flatten', action='store_true', help='Composite to white')

args = argparser.parse_args()

im = Image.open(args.input_file)
path_to_im = path.dirname(args.input_file)
im_filename_without_extension = path.splitext(path.basename(args.input_file))[0]
im = im.convert('RGBA')
settings = {}

if not args.top and not args.right and not args.bottom and not args.left:
    settings['sides'] = ['Right', 'Bottom']
else:
    settings['sides'] = []
    if args.top:
        settings['sides'].append('Top')
    if args.right:
        settings['sides'].append('Right')
    if args.bottom:
        settings['sides'].append('Bottom')
    if args.left:
        settings['sides'].append('Left')
settings['fadesize'] = args.fade_size

fadesize = settings['fadesize']

if fadesize % 2 is not 0:
    fadesize = int(fadesize)
    fadesize += 1


def draw_top(pic, drawpic, border_size):
    drawpic.rectangle([0, 0, pic.size[0], border_size * 3], fill='black')


def draw_right(pic, drawpic, border_size):
    drawpic.rectangle([pic.size[0] - border_size * 3, 0, pic.size[0], pic.size[1]], fill='black')


def draw_bottom(pic, drawpic, border_size):
    drawpic.rectangle([0, pic.size[1] - border_size * 3, pic.size[0], pic.size[1]], fill='black')


def draw_left(pic, drawpic, border_size):
    drawpic.rectangle([0, 0, border_size * 3, pic.size[1]], fill='black')


im_mask = Image.new('L', (im.size[0] + fadesize, im.size[1] + fadesize), 'white')

drawmask = ImageDraw.Draw(im_mask)

if 'Top' in settings['sides']:
    draw_top(im_mask, drawmask, fadesize)
if 'Right' in settings['sides']:
    draw_right(im_mask, drawmask, fadesize)
if 'Bottom' in settings['sides']:
    draw_bottom(im_mask, drawmask, fadesize)
if 'Left' in settings['sides']:
    draw_left(im_mask, drawmask, fadesize)

im_mask_blur = im_mask.filter(ImageFilter.GaussianBlur(radius=fadesize))

im_mask_blur_crop = im_mask_blur.crop(box=(int(fadesize / 2), int(fadesize / 2),
                                           im_mask_blur.size[0] - int(fadesize / 2),
                                           im_mask_blur.size[1] - int(fadesize / 2)
                                           )
                                      )

im_with_alpha = im.putalpha(im_mask_blur_crop)

output_filename = im_filename_without_extension + '_faded.png'
output_path = path.join(path_to_im, output_filename)

if args.flatten:
    background = Image.new("RGB", im.size, (255, 255, 255))
    background.paste(im, mask=im.split()[3])
    background.save(output_path)
else:
    im.save(output_path)
print('File saved to: ' + output_path)
