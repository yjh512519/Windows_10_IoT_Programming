using System;
using Windows.ApplicationModel.Background;
using Windows.Devices.Gpio;

namespace ButtonInputBgApp
{
    public sealed class StartupTask : IBackgroundTask
    {
        private const int buttonPinNumber = 5;
        private const int ledPinNumber = 47;

        private const int debounceTime = 20;

        private GpioPin ledPin;

        private BackgroundTaskDeferral bgTaskDeferral;

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            bgTaskDeferral = taskInstance.GetDeferral();

            var buttonPin = ConfigureGpioPin(buttonPinNumber, GpioPinDriveMode.InputPullUp);
            if (buttonPin != null)
            {
                buttonPin.DebounceTimeout = TimeSpan.FromMilliseconds(debounceTime);
                buttonPin.ValueChanged += ButtonPin_ValueChanged;

                ledPin = ConfigureGpioPin(ledPinNumber, GpioPinDriveMode.Output);
                if (ledPin != null)
                {
                    ledPin.Write(GpioPinValue.Low);
                }
            }
            else
            {
                bgTaskDeferral.Complete();
            }
        }

        private GpioPin ConfigureGpioPin(int pinNumber, GpioPinDriveMode driveMode)
        {
            var gpioController = GpioController.GetDefault();

            GpioPin pin = null;
            if (gpioController != null)
            {
                pin = gpioController.OpenPin(pinNumber);
                if (pin != null)
                {
                    pin.SetDriveMode(driveMode);
                }
            }

            return pin;
        }

        private GpioPinValue InvertGpioPinValue(GpioPin gpioPin)
        {
            var currentPinValue = gpioPin.Read();

            GpioPinValue newPinValue;

            if (currentPinValue == GpioPinValue.High)
            {
                newPinValue = GpioPinValue.Low;
            }
            else
            {
                newPinValue = GpioPinValue.High;
            }

            return newPinValue;
        }

        private void ButtonPin_ValueChanged(GpioPin sender, GpioPinValueChangedEventArgs args)
        {
            if (ledPin != null)
            {
                var newValue = InvertGpioPinValue(ledPin);

                ledPin.Write(newValue);
            }
        }
    }
}
