# Myfi
 Onelick to show saved wifi (SSID and Password) 

```
using System.Diagnostics;
using System.Text.RegularExpressions;

Process processWifi = new Process();
processWifi.StartInfo.WindowStyle = ProcessWindowStyle.Hidden;
processWifi.StartInfo.FileName = "netsh";
processWifi.StartInfo.UseShellExecute = false;
processWifi.StartInfo.RedirectStandardError = true;
processWifi.StartInfo.RedirectStandardInput = true;
processWifi.StartInfo.RedirectStandardOutput = true;
processWifi.StartInfo.CreateNoWindow = true;

processWifi.StartInfo.Arguments = "wlan show profile";
processWifi.Start();
string wifi_result = processWifi.StandardOutput.ReadToEnd();
processWifi.WaitForExit();

Console.ForegroundColor = ConsoleColor.Red;
Console.WriteLine("-----------------------------------");
Console.WriteLine(string.Format("{0}{1}", "SSID".PadRight(20), "Password".PadRight(15)) + "\r");
Console.WriteLine("-----------------------------------");
using (StringReader reader_wifi = new StringReader(wifi_result))
{
    string? line_wifi;
    while ((line_wifi = reader_wifi.ReadLine()) != null)
    {

        Regex regex = new Regex(@"All User Profile * : (?<after>.*)");
        Match match = regex.Match(line_wifi);
        if (match.Success)
        {
            string current_name = match.Groups["after"].Value;

            string password = "";

            processWifi.StartInfo.Arguments = "wlan show profile name=\"" + current_name + "\" key=clear";
            processWifi.Start();
            string get_password = processWifi.StandardOutput.ReadToEnd();
            processWifi.WaitForExit();
            using (StringReader reader_pass = new StringReader(get_password))
            {
                string? line_pass;
                while ((line_pass = reader_pass.ReadLine()) != null)
                {
                    Regex regex2 = new Regex(@"Key Content * : (?<after>.*)");
                    Match match2 = regex2.Match(line_pass);

                    if (match2.Success)
                    {
                        password = match2.Groups["after"].Value;
                    }
                }
            }

            if (password == "")
            {
                Console.ForegroundColor = ConsoleColor.White;
            }
            else
            {
                Console.ForegroundColor = ConsoleColor.Green;
            }
            Console.WriteLine(string.Format("{0}{1}", current_name.PadRight(20), password.PadRight(15)) + "\r");
        }

    }
}
Console.ForegroundColor = ConsoleColor.Red;
Console.WriteLine("-----------------------------------");

Console.ReadLine();
```
