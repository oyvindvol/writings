# UIImage fade in animation

Simple animations like fading in images when they are available brings life to an iphone application, and is a simple
action to improve the quality feel of your application.

```objective-c
  UIImageView *imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"test-image"]];
  imageView.alpha = 0.0;
  [UIView animateWithDuration:0.7 animations:^{
    imageView.alpha = 1.0;
  }];
```
