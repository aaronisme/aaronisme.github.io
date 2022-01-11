---
layout: post
title: "ios-alert-share"
date: "2015-12-25 20:09"
categories: Learning
tags:
    - IOS
---
# IOS-Summary-Alert-Activiy-Share-web-API-week2

## Create an Alert Action In IOS
1. first you should create an AlertController
2. Create an Action
3. Add the Action to the AlertController
4. present the Controller to the view.
```objectivec

-(void)showAlertMessage:(NSString *) myMessage{
    UIAlertController *signInActionController = [UIAlertController alertControllerWithTitle:@"" message:myMessage preferredStyle:UIAlertControllerStyleAlert];
     //Create an AlertViewController first

    UIAlertAction *canle = [UIAlertAction actionWithTitle:@"okay" style:UIAlertActionStyleDefault handler:nil];

     //Create an Action then
    [signInActionController addAction:canle];
    //Add the Action
    [self presentViewController:signInActionController animated:YES completion:nil];
     //present that action in the view.
}

```
## Make the Activity Share(IOS自带的share集合和工具)
```objectivec

    UIActivityViewController *activityVC = [[UIActivityViewController alloc] initWithActivityItems:@[self.activityTextField.text] applicationActivities:nil];
    [self presentViewController:activityVC animated:YES completion:nil];
// based on the acitivityitems type to decide which app to share, if I change to self.actitivyTextField. it is an object so no app can accept that share!
//根据items的类型来确定能share给的app
```
## 单一分享：
```objectivec

    UIAlertAction *tweet = [UIAlertAction actionWithTitle:@"Tweet" style:UIAlertActionStyleDefault  handler:
                            ^(UIAlertAction *action){
                                   //first check whether logged in?
                                if ([SLComposeViewController isAvailableForServiceType:SLServiceTypeSinaWeibo]) {
                                   SLComposeViewController *twitterVC = [SLComposeViewController composeViewControllerForServiceType:SLServiceTypeSinaWeibo];
                                    //then create the viewController
                                    if ([self.textField.text length] <140) {
                                   //add the text to share
                                        [twitterVC setInitialText:self.textField.text];
                                    }else{
                                        NSString *shortText = [self.textField.text substringToIndex:140];
                                        [twitterVC setInitialText:shortText];
                                    }
                                   // present the view
                                    [self presentViewController:twitterVC animated:YES completion:nil];

                                }else{
                                    [self showAlertMessage:@"Please Sign in your Twitter"];
                                }
    }];

```

IOS-SDK自带了四种Social的平台。
同样是4步：see the comments

## Social Sharing —OAuth2

1. import the NXOAuth2Client from Cocopods:
![process-chart](/img/in-post/ios/process-chart.jpg)

1. 首先initAccountStore：
2. request Access
3. web redirect to the custom URL
4. app handle the URL


```objectivec
//init the accountstore
[[NXOAuth2AccountStore sharedStore] setClientID:@"61c99c80e0324c7992e87aa575ce582e"
                                             secret:@"93f0359abc3c4c119780c027d0b1d447"
                                   authorizationURL:[NSURL URLWithString:@"https://api.instagram.com/oauth/authorize"]
                                           tokenURL:[NSURL URLWithString:@"https://api.instagram.com/oauth/access_token"]
                                        redirectURL:[NSURL URLWithString:@"grammyplus://things.com"]
                                     forAccountType:@"Instagram"];

```
```objectivec
// this is the callback function for redirect url,web give the custom url for app step4 and this function is defined in the AppDelegate.m
-(BOOL)application:(UIApplication *)application openURL:(nonnull NSURL *)url options:(nonnull NSDictionary<NSString *,id> *)options
{
    NSLog(@"we recieved the login callback");
    return [[NXOAuth2AccountStore sharedStore] handleRedirectURL:url];

}
```
##How to make an request:
```objectivec
//fisrt compose the URL
        NSString *imgUrlStr = pkg[@"data"][0][@"images"][@"standard_resolution"][@"url"];

        NSURL *imgUrl= [NSURL URLWithString:imgUrlStr];
//set the request. note function is:
//[[session dataTaskwithURL:completeHandler:^(){}] resume] the handler is an block function!            
        [[session dataTaskWithURL:imgUrl completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
                if (error) {
                    NSLog(@"Error: net request error");
                    return ;
                }
                //error2
                NSHTTPURLResponse *serverResonse = (NSHTTPURLResponse *)response;

                if(serverResonse.statusCode <200 || serverResonse.statusCode >= 300){
                    NSLog(@"HTTP Error");
                    return;
                }
	            //取得绘制UI的主线程进行绘制
                dispatch_async(dispatch_get_main_queue(), ^{
                    self.ImageView.image = [UIImage imageWithData:data];

                });




                }]resume];
```
