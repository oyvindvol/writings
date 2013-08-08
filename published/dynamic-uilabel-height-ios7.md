# Dynamic UILabel height in iOS 7

After struggling with increasing the height of my UILabel in a UITableViewCell dynamically according to the amount of
text inside it, i finally found a (simple) solution to this. 

## `sizeWithFont:constrainedToSize:lineBreakMode` is deprecated
First of all, the way I used to solve this by using `sizeWithFont:constrainedToSize:lineBreakMode` to return me a
`CGSize` object with height and width properties, is deprecated in iOS 7. The way to do it now is sending the 
`boundingRectWithSize:options:attributes:context` message to the UILabel in question, returning a `CGRect` object with
a `CGSize` property containing the expected height and with of the UILabel.

## Let the cell implementation do the resizing
Previously, I have always done the operation of resizing labels in cells within the view controller implementation in the
UITableViewControllerDelegate method `tableView:cellForRowAtIndexPath`. However, I think the proper way to implement it,
is by making the custom cell implementation do the resizing and layout of its UILabel instance. Also, I have experienced 
problems with resizing if I use Storyboards and `IBOutlet`. The code below shows how I implemented it without using 
a `IBOutlet` in my storyboard, just placing the frame where I know I want my UILabel to appear in the UITableViewCell. 
I overrode `layoutSubViews:` in my custom subclass:

CustomTableViewCell.h
```objective-c
@interface CustomTableViewCell : UITableViewCell

@property (nonatomic, strong) UILabel *heading;
@property (nonatomic, strong) UIImageView *image;

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
    [super layoutSubviews];
    CGSize expectedSize = [_heading.text boundingRectWithSize:CGSizeMake(151, 104) options:(NSStringDrawingUsesLineFragmentOrigin) attributes:@{NSFontAttributeName: _heading.font} context:nil].size;
    _heading.frame = CGRectMake(136, 8, 151, expectedSize.height);
    [self addSubView:_heading];
}

@end
```

And the UITableViewController

```objective-c
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    CustomTableViewCell *cell = (CustomTableViewCell *)[self.tableView dequeueReusableCellWithIdentifier:@"CustomCell"]; 
    cell.heading.text = @"Some long text...";
    cell.image.image = [UIImage imageNamed:@"someImage"];
    
    return cell;
}
```
