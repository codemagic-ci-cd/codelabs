---
id: your-first-flutter-app-to-appstore
summary: In this tutorial, you will be going through all the steps necessary to build, sign and deploy a Flutter app to the Apple App Store.
status: [draft]
authors: Maksim Lin
categories: web
tags: codemagic,flutter
feedback_link: https://github.com/codemagic-ci-cd/codelabs/issues
source: 1fDRVc23lN8wi65TuxrLLhIGNG1huqCi7fHnv_9vD-oc
duration: 0

---

# Publish your first Flutter app to the App Store

[Codelab Feedback](https://github.com/codemagic-ci-cd/codelabs/issues)

## Introduction

**Last Updated:** 2023-05-19

In this tutorial, you will be going through all the steps necessary to build, sign and deploy a Flutter app to the Apple App Store.

### **What you'll build**

* A basic new Flutter app 
* Create a single workflow project for it on Codemagic
* Signed IPA file that can be installed on devices directly, via TestFlight and submitted for review to the Apple App Store.

> aside negative
> 
> **Note:** To simplify this tutorial, and explain the fundamentals of the process of publishing your first Flutter app to the App Store with CI/CD, we will use Codemagic's Workflow Editor, though you can switch to using YAML for your projects workflow configuration afterwards if you wish.

### **What you'll learn**

* How to create a new Flutter app project on Codemagic
* How to setup an account on Apple App Store connect
* How to build and sign your iOS app
* How to deploy your app to Apple TestFlight and the App Store

### **What you'll need**

* The Flutter SDK installed on your Windows, Linux or macOS computer.
* A  ***PAID*** Apple Developer account
* A GitHub (or GitLab or Bitbucket) account
* A Codemagic account
* An iOS device


## Getting set up

If you don't yet have an account with a Git provider, you can create  [a new one with GitHub here](https://github.com/signup).

If you don't yet have a **PAID** Apple Developer account, you can  [begin the process of creating a new one here](https://developer.apple.com/programs/enroll/). Note that you will need to provide Apple with a number of pieces of information and pay (as of the time of this writing) $99USD per year for your account (or the equivalent for your country). Once you get to the end of the process and see a screen like this:

<img src="img/65c9ba1a02a555a9.png" alt="screenshot of Apple developer site showing confirmation to go ahead with purchase of Apple Developer Program yearly membership"  width="624.00" />

you are almost finished with the process.

Confusingly, Apple splits the functionality required to manage your developer and app information across both the Apple Developer website and the App Store Connect website, and the processes and user interface on those sites are often convoluted and confusing to newcomers, so as much as possible, this tutorial will make use of Codemagic's functionality to keep to an absolute minimum the interactions required with both those sites.


## Your Flutter app

In order to publish your app to the App Store, you will first need an app! 

Before we begin, make sure you are using the latest current version of the Flutter SDK stable channel:

```
flutter --version
Flutter 3.10.0 • channel stable • https://github.com/flutter/flutter.git
Framework • revision 84a1e904f4 (7 days ago) • 2023-05-09 07:41:44 -0700
Engine • revision d44b5a94c9
Tools • Dart 3.0.0 • DevTools 2.23.1
```

The above specific versions are not important, but what you want to make sure is that the output includes the text:  **`• channel stable •`**  

as per the example above.

For the purposes of this tutorial, we will use a Flutter app that is closely based on the "Counter app" that is created for you when you run Flutters `create` command.

You can do that now:

```
flutter create classic_counter
```

### **Naming your app**

One of the parts to creating our app that will be essential later on in the process of publishing it on TestFlight and the App Store is to create a unique identifier for it. On Apple platforms, this identifier is the ***Bundle ID***.

> aside negative
> 
> **Note:** The Bundle ID must be ***unique***, so make sure you pick a ID for your project that will be unique to it and not use the one used in examples here or that is created in the default Flutter app scaffold for you with the create command! A good recommendation is to use a reverse domain name, so if we have `codemagic.io` then we would use as the Bundle ID: `io.codemagic.classicCounter` for example.

Per the instructions in the  [Flutter documentation](https://docs.flutter.dev/deployment/ios#register-a-bundle-id), you will first need to go to the Apple Developer site, login and begin the process of creating a new app identifier:

<img src="img/a6e6eb33ea090d7b.png" alt="screenshot of certificates, identifiers & profiles screen of Apple developer website"  width="624.00" />

Subsequent steps in the process will ask you to choose the identifier type:

<img src="img/1d2f04611656800b.png" alt="screenshot of register new identifier from Apple developer website"  width="624.00" />

and finally enter your chosen Bundle ID:

<img src="img/f3860888fd5016d0.png" alt="screenshot of register an app section from Apple developer website"  width="624.00" />

Once you have completed all the steps in creating the app identifier, you should see it listed under your Apple Developer account under the `Identifiers` section:

<img src="img/d67f319fd183582.png" alt="screenshot of certificates, identifiers & profiles screen of Apple developer website"  width="624.00" />

Now we need to follow the  [next process of creating an application record on App Store Connect](https://docs.flutter.dev/deployment/ios#create-an-application-record-on-app-store-connect).

First select "My Apps":

<img src="img/56ae3953fb2dcee0.png" alt="screenshot of My apps section of Apple appstore connect website"  width="624.00" />

Then click on the plus icon to add a new entry:

<img src="img/b7df48b0ad999dd8.png" alt="screenshot of arrow pointing to plus button of My apps section of Apple appstore connect website"  width="624.00" />

Select "New App" from the drop down and then fill in the form shown:

<img src="img/31203a38e6b4e7e9.png" alt="screenshot of new app form of Apple appstore connect website"  width="624.00" />

Note that in the "Bundle ID" drop down you should see and be able to select the bundle id you created previously in the Apple Developer website. 

You now need to update your Flutter app code to use the Bundle ID you have chosen. The easiest way to do this is using Xcode and you can follow the instructions for how to do that from  [the Flutter documentation](https://docs.flutter.dev/deployment/ios#review-xcode-project-settings).

Now that you have updated your app, you will need to add it to a Git repository and push that repository to a Git hosting provider (eg. GitHub) ***after*** you have created a new repository with the hosting provider:

```
cd classic_counter
git init .
git commit -a -m "app scaffold"
git add remote origin <git-hosting-provider-url>
git push origin main
```


## Building your iOS app on Codemagic



You will start by initially building your app for iOS *without* code signing on Codemagic. 

To setup your app on Codemagic you need to create the project by connecting a Git hosting provider (eg. GitHub):

<img src="img/b570fd1c91941b08.png" alt="screenshot of connect repository screen of Codemagic website"  width="624.00" />

Then select a repository and the Flutter project type from there:

<img src="img/7532d118d39a0aa8.png" alt="screenshot of setup application screen of Codemagic website"  width="624.00" />

You then need to configure a few settings for your new app project on Codemagic. First check that only iOS is selected as the platform:

<img src="img/f38da4d8bd06730.png" alt="enlarged screenshot of Build for platforms config section of Codemagic website"  width="624.00" />

Then that the versions are set to `Stable Channel`, `Latest Xcode` and `default Cocoapods`:

<img src="img/23f73dab9609a4d4.png" alt="enlarged screenshot of tool versions config section of Codemagic website"  width="624.00" />

And that the build `Mode` is set to `Debug`:

<img src="img/49d644fc5f5b357b.png" alt="enlarged screenshot of Mode config section of Codemagic website"  width="624.00" />

Once you have made those changes to the settings, click Save changes button in the top right hand corner:

<img src="img/9bed8f7a579608af.png" alt="enlarged screenshot of tool Save button on Codemagic website"  width="501.00" />

and then you can then start the your first build by clicking on the `Start first build` button:

<img src="img/e2cc80076a7bf15e.png" alt="enlarged screenshot of tool 'start your first build' button on Codemagic website"  width="501.00" />

After your first build, this button will change to be `Start new build`:

<img src="img/e025ea86cc445862.png" alt="enlarged screenshot of tool 'start new build' button on Codemagic website"  width="624.00" />

Once your build successfully completes, you will have an **unsigned** artifact that you will be able to run *only* on the iOS Simulator.


## Signing your iOS app on Codemagic



Codemagic supports both "automatic" and "manual" processes to code sign you app. We will only describe using automatic code signing in this tutorial as it is by far the easier process, especially for newcomers to iOS code signing.

### **What is code signing?**

But what is code signing for apps in the first place? 

In general terms, code signing is a cryptographic process by which certificates, which themselves are made up of corresponding public and private keys, can be used to create a "hash" value of some data (eg. the executable code and resources that make up an app). The  private key is used in such a way that we can later use the public key to confirm that only someone possessing the private key could have created (calculated) that hash value for the given app.

In the context of Apple and iOS apps, code-signing is the process by which Apple ensures that  [only apps that it has explicitly authorized are able to be executed on Apple platforms such as iOS devices](https://developer.apple.com/documentation/technotes/tn3125-inside-code-signing-provisioning-profiles#Provisioning-profile-fundamentals). The way this is achieved is through the use of **provisioning profiles**. Provisioning profiles are files that can only be created by the Apple Developer website which specify for an app things such as who can sign the app, where those apps can run and what the apps are entitled to do by storing information such as the certificate that can be used to sign the app, the apps unique identifier and other app metadata such as the unique IDs of the devices the app is allowed to run on. 

The reason that only the Apple Developer website is able to create provisioning profiles is that the profiles *themselves* are signed by Apple, using a certificate they hold. 

As you might be able to tell from the above, the Provisioning Profile lies at the heart of the code signing process on iOS (and in fact all Apple platforms) and much more details about them can be found at in the  [Apple technote on the subject](https://developer.apple.com/documentation/technotes/tn3125-inside-code-signing-provisioning-profiles).

An important point to note is that there can (and likely will) be ***multiple*** provisioning profiles per each of your apps. For example the Ad-Hoc provisioning profiles are only intended for you to use when you are building your app to distribute to a small number of users (or testers) directly and you choose device UDID's you would need to add directly into the Apple Developer portal. While the App Store profile is used for uploading your app build to App Store Connect, for distribution via Test Flight or finally via the public App Store itself.

Luckily for the purposes of this tutorial, most of the details of dealing with obtaining, managing and using Provisioning profiles can be handled by Codemagic via the App Store Connect API. But in order to allow Codemagic to do so on behalf of your app, you first need to obtain an App Store Connect API **key**. The process to do so is:

1. Log in to App Store Connect and navigate to Users and Access &gt; Keys.
2. Click on the + sign to generate a new API key.
3. Enter the name for the key and select an access level. We recommend choosing App Manager, considering that Developer does not have the required permissions to upload to the store. Read more about Apple Developer Program role permissions here.
4. Click Generate.
5. As soon as the key is generated, you can see it added to the list of active keys. Click Download API Key to save the private key for later. Note that the key can only be downloaded once.

This process is also covered in   [this section of the Codemagic documentation](https://docs.codemagic.io/flutter-code-signing/ios-code-signing/#step-1-creating-an-app-store-api-key-for-codemagic).  Once you have obtained an API key, you will need to enter it, along with the **`Issuer ID`** and **`Key ID`** both of which can be found on the App Store Connect website:

<img src="img/4250335c4af2b337.png" alt="screenshot of App Store Connect API section of apple appstore connect website"  width="624.00" />

and then add them into your team/account section in the Codemagic webapp:

<img src="img/e56038fa60a5734c.png" alt="screenshot of tool Teams section of the Codemagic website"  width="624.00" />

<img src="img/e352babb6b569dfb.png" alt="enlarged screenshot of tool Teams config section on Codemagic website"  width="624.00" />

If you prefer to a see a demonstration, the above process of how to obtain and use the API key  [is covered in this video](https://www.youtube.com/watch?v=aBDx6BKFXIA).

#### **Registering your test devices**

You will want to test your app on at least one actual iOS device, so you will need to register that device on your Apple Developer account **prior** to building your first **signed** iOS artifact. 

To add your devices to your profile, you will need to go to the `Devices` section of the Apple Developer web console and click on the plus button:

<img src="img/ae4c520e5ae2cbb1.png" alt="enlarged screenshot of Devices plus button from Certificates, Indentifiers & Profiles section of Apple developer website"  width="624.00" />

Specific instructions on how to obtain the required UUID of each of your iOS devices can be found  [in the Apple developer documentation](https://developer.apple.com/documentation/xcode/distributing-your-app-to-registered-devices#Collect-device-identifiers-iOS-iPadOS-tvOS-watchOS).

Now, moving back to the ***Codemagic web interface***: in your app workflow editor configuration, you will need to go to the Distribution section and make sure that the code signing method is set to `Automatic` and that the `App Store Connect API key` and `Bundle Identifier` match those that you set up in the previous parts of this step:

<img src="img/317795e2c5d5b51b.png" alt="enlarged screenshot with red arrow pointing to 'Select code  signing method' on Codemagic website"  width="624.00" />

Now that you have done all the required preparation, you can start a new build of your app on Codemagic using the start new build button as you did previously and once it completes successfully, you will have a **signed** artifact that you can download and install to your iOS device: 

<img src="img/9722d6d028b7de23.png" alt="screenshot of Codemagic website with red arrow pointing to link on the page to generated IPA file link"  width="624.00" />

> aside negative
> 
> **Note:** Codemagic provides a very handy feature, where by clicking on the QR code icon next to the IPA artifact file name you will get a QR code with the download link to the file, very handy to quickly download and install it with your iPhone or iPad.


## Uploading your app to TestFlight



Now that you have a working signed build, the next step is to upload it to TestFlight to make it more easily available to others to install and test your app. 

To set up uploading to TestFlight you will need to again go to the `Distribution` section of your apps workflow editor in Codemagic, but this time open the `App Store Connect` instead of the `iOS code signing` subsection:

<img src="img/a14e4906c3d0bc4f.png" alt="screenshot with red arrow pointing to App Store Connect [enabled] section of Codemagic website"  width="624.00" />

In that section you will need to ensure that both the Enable App Store Connect publishing and Submit to TestFlight beta review items are enabled:

<img src="img/ac68f5b08c27084c.png" alt="screenshot with red arrow pointing to 'Enable App Store Connect publishing' checkbox and another red arrow pointing to 'Submit to Testflight beta review` checkbox of the Codemagic website"  width="624.00" />

Once the build workflow successfully completes, if you look in App Store Connect website, you should now see your app build listed there, under the `TestFlight` tab:

<img src="img/50885fe759b82c1e.png" alt="screenshot of the Apple App store connect website showing the app listed in the iOS builds section of the website"  width="624.00" />

On your iOS device, you will need to open the App Store app and install the TestFlight app in order to be able to install your app.


## Publishing your app to the App Store



Finally, after you have iterated on making and testing changes to your app and you are ready to publish your app to the public on the App Store, the final step of submitting your app for review to Apple just requires you to run your build on Codemagic again, but this time just enabling the option under the Codemagic  `App Store Connect` subsection to submit the app for review:

<img src="img/9fb89e03e152665e.png" alt="screenshot of Codemagic website with red arrow pointing to the 'Submit to App Store review' checkbox"  width="624.00" />


## Next Steps

Congratulations! 

You have now published your first Flutter app to the Apple App Store!

### **What we've covered**

✅ Created a new Flutter app project on Codemagic

✅ Setup an account on Apple App Store connect

✅ Built and signed your iOS app

✅ Deployed your app to Apple TestFlight and the App Store

### **What next?**

* Configure build versioning by switching to using a YAML configuration file stored in your Git repository
* Duplicate your workflows to keep release and development workflows separated
* Add more platforms to your project on Codemagic such as Android, MacOS, Linux, Windows and Web to make your app available to users on all those platforms


