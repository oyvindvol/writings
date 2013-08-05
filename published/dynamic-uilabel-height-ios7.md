# Dynamic UILabel height in iOS 7

After struggeling with increasing the height of my UILabel in a UITableViewCell dynamically according to the amount of
text inside it, i finally found a (simple) solution to this. 

## `sizeWithFont:constrainedToSize:lineBreakMode` is deprecated
First of all, the way I used to solve this by using `sizeWithFont:constrainedToSize:lineBreakMode` to return me a
`CGSize` object with height and width properties, is deprecated from iOS 7. The way to do it now is sending the 
`boundingRectWithSize:options:attributes:context` message to the UILabel in question, returning a `CGRect` object with
a `CGSize` property containing the height and with of the UILabel.

## Let the cell implementation do the resizing
Previously, I have always done the operation of resizing labels in cells within the view controller implementation in the
UITableViewControllerDelegate method `tableView:cellForRowAtIndexPath`. This has worked fine in the past, but
somehow this was not working for me in iOS 7. Instead, I went inside my custom UITableViewCell subclass I had created for
my table view cells, and overrided `layoutSubviews` to set the height of the UILabel.

CustomTableViewCell.h
```objective-c
@interface CustomTableViewCell : UITableViewCell

@property (nonatomic, strong) UILabel *heading;
@property (nonatomic, strong) IBOutlet UIImageView *image;

@end
```

CustomTableViewCell.m
```objective-c
#import "CustomTableViewCell.h"

@implementation CustomTableViewCell

- (id)initWithCoder:(NSCoder *)aDecoder
{
    self = [super initWithCoder:aDecoder];
    if (self) {
        _heading = [[UILabel alloc] init];
        _image = [[UIImageView alloc] init];
    }
    return self;
}

- (void)layoutSubviews
{
    CGSize expectedSize = [_heading.text boundingRectWithSize:CGSizeMake(151, 104) options:(NSStringDrawingUsesLineFragmentOrigin) attributes:@{NSFontAttributeName: _heading.font} context:nil].size;
    _heading.frame = CGRectMake(136, 8, 151, expectedSize.height);
}

@end
```
