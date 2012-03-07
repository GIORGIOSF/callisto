# Callisto

## What Is It?

Callisto is a library for use in Windows 8 XAML applications (aka Metro style apps).  The XAML framework in Windows.UI.Xaml is great, but has some functionality that isn't provided in-the-box in a few controls and APIs.  Callisto serves to provided added functionality on top of the XAML UI framework for Windows.

## What's In It?
So far Callisto includes:

* `Flyout` - a primitive that includes positioning and 'light dismiss' logic
* `Menu` - primarily to be used from AppBar, contains the base for providing, well, a Menu
* `MenuItem` - an item for a menu, including separators and contains the command point for the menu item

What is in the plan:

* `Settings` - a panel for settings implementation from the charms bar
* `Pivot` - a UI interface for providing "tabbed" interface in a Metro style way
* `ThemeSwitcher` - to dynamically switch themes for certain UI components

## How To Install It?
There are two ways you can install the control.  

### Visual Studio Extension SDK
If you download the ZIP file it is formatted as an "Extension SDK" for Visual Studio 2011.  You would extract the contents of this ZIP file to:

	%ProgramFiles%\Microsoft SDKs\Windows\v8.0\ExtensionSDKs

Once placed there, the control will be available for all projects on your machine via the Add Reference dialog in Visual Studio under the Windows\Extensions category.

### NuGet
You can get the control via [NuGet](http://www.nuget.org) if you have the extension installed for Visual Studio or via the PowerShell package manager.  This control is published via NuGet at [Callisto](http://timheuer.com/).

## How To Use It?
To use the controls you simply create an instance of them (we will use an example here) like `Flyout` and tell it what content goes in it and where it should go:

	using Callisto.Controls;
	
	Flyout f = new Flyout();
	
	// Flyout is a ContentControl so set your content within it.
	f.Content = new Border() { Width=500, Height=500 };
	
	f.Placement = PlacementMode.Top;
	f.PlacementTarget = MyButton; // this is an UI element (usually the sender)
	
	f.IsOpen = true;

You can also use the Menu as content:

	using Callisto.Controls;
	
	Flyout f = new Flyout();
	
	f.Placement = PlacementMode.Top;
	f.PlacementTarget = MyButton; // this is an UI element (usually the sender)
	
	Menu m = new Menu();
	
	MenuItem mi1 = new MenuItem();
	mi1.Text = "Some Option";
	
	MenuItem mi2 = new MenuItem();
	mi2.Text = "Another Option Here";
	
	m.Items.Add(mi1);
	m.Items.Add(new MenuItemSeparator());
	m.Items.Add(mi2);
	
	f.Content = m;
	
	f.IsOpen = true;

This creates a menu flyout for command from an `AppBar` control for example.

### Using with content that gathers input
If you are using the Flyout with a `UserControl` that perhaps would gather input, you need to take account the fact that the input host manager (IHM) or 'soft keyboard' will show up in touch situations.  An un-parented `Flyout` control will not automatically scroll into view when the soft keyboard shows up.  This means that if you have input on the bottom `AppBar` as an example, your input would be hidden because the soft keyboard will likely cover it.  

This is solvable by adding the `Flyout` to the view.  You must now, however, manage the removal of it so you don't get a leak.  Here is a pseudo example

	using Callisto.Controls;
	
	Flyout f = new Flyout();
	
	// other stuff here
	
	LayoutRoot.Children.Add(f.HostPopup); // add this to some existing control in your view like the root visual
	
	// remove the parenting during the Closed event on the Flyout
	f.Closed += (s,a) =>
		{
			LayoutRoot.Children.Remove(f.HostPopup);
		};

This should prevent a leak of the object when it is dismissed.

## Commands and Clicks
When using just the `Flyout`, you own the Content within it.  When using the `Menu` and `MenuItem` you can attach an event to the `Tapped` event in `MenuItem` or also by providing a command that has implemented `ICommand` in your code.

## I found an Issue
Great, please [log a bug](https://github.com/timheuer/Callisto/issues/new) so that it can be tracked. 

## Credits
* [Tim Heuer](http://timheuer.com/blog/) ([@timheuer](http://twitter.com/timheuer)) - for the initial creation of the control.
* [Silverlight Toolkit](http://silverlight.codeplex.com) - for providing the base of many ported concepts/code...yay Open Source!