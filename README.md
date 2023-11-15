# The Historical Archive of Digital Storages in Terraria

[![144BA](https://github-production-user-asset-6210df.s3.amazonaws.com/33048298/282906081-af2ea8cc-42d1-41cb-9612-1ffc469181bc.png)](https://www.youtube.com/watch?v=bE96Vxmg8Xw "Terraria largest possible pixelbox display - Click to Watch!")

## **RAM (1 bit each ~4 tiles)**
**NOTES:**
 - The densest possible RAM available in Terraria.
 - Data is written vertically.

**REAL WORLD USES:**
 - [Terraria: (Remastered) 24x24 Pixelbox Bad Apple!!](https://www.youtube.com/watch?v=35gh2iNliKQ)

**SAMPLE PROGRAMMING CODE:**
<details><summary>Show Code</summary>
 
```c#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Windows.Forms;
using Microsoft.Xna.Framework;
using Terraria.Chat.Commands;
using Terraria.Localization;

namespace Terraria.Chat
{
	public partial class ChatCommandProcessor : IChatProcessor
	{
		public void ProcessIncomingMessage(ChatMessage message, int clientId)
		{
			// ###################################################################################
			if (message.Text.Contains("/arom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				bool useGap = true;
				
				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2((Main.LocalPlayer.position.X / 16) + 2, Main.LocalPlayer.position.Y / 16); // Offset 2 right.
				Vector2 wirePosition = playerPosition;
				
				int lineOffset = 1;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.
					
					// Define variables based on the list of words.
					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								foreach (char bit in Line)
								{
									// If bit is a 1 then place wire.
                                    if (bit.ToString() == "0")
                                    {
                                        // Place on lamp.
										Main.tile[(int)wirePosition.X, (int)wirePosition.Y].type = 419;
							            Main.tile[(int)wirePosition.X, (int)wirePosition.Y].frameX = 0;
                                    }
									else if (bit.ToString() == "1")
                                    {
                                        // Place faulty lamp.
										Main.tile[(int)wirePosition.X, (int)wirePosition.Y].type = 419;
							            Main.tile[(int)wirePosition.X, (int)wirePosition.Y].frameX = 18;
                                    }

									wirePosition.Y += 3;
								}
								
								// Progress position right.
								wirePosition.Y = playerPosition.Y;
								
								// Check how much to offset right.
								if (useGap)
								{
								    useGap = false; // Toggle gap.
								    wirePosition.X += 2;
								}
								else
								{
								    useGap = true; // Toggle gap.
								    wirePosition.X += 1;
								}
							}

							// Display Console
							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}
			
			IChatCommand chatCommand;
			if (this._commands.TryGetValue(message.CommandId, out chatCommand))
			{
				chatCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
				return;
			}

			if (this._defaultCommand != null)
			{
				this._defaultCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
			}
		}
	}
}
```
</details>

![RAM1](https://github.com/RussDev7/TerrariaDigitalStorage/assets/33048298/4eafa669-5ab4-4451-8220-50534bb197ea)

## **Faulty Gate ROM (1 bit each 1 tile)**
**NOTES:**
 - The only storage mechanism that can store 1 bit per 1 tile.
 - Data is stored horizontally.

**REAL WORLD USES:**
 - [v1: Terraria But Its Animated Shrek](https://www.youtube.com/watch?v=EVP2zqgrtzg)

**SAMPLE PROGRAMMING CODE:**
<details><summary>Show Code</summary>
 
```c#
//###################################################################################
//Terraria.Chat > ChatCommandProcessor (OR CreateOutgoingMessage)
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using Microsoft.Xna.Framework;
using Terraria.Chat.Commands;
using Terraria.Localization;
using Microsoft.Xna.Framework.Graphics;
using ReLogic.Content;

namespace Terraria.Chat
{
	public partial class ChatCommandProcessor : IChatProcessor
	{
		public void ProcessIncomingMessage(ChatMessage message, int clientId)
		{
			// ###################################################################################
			if (message.Text.Contains("/arom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = 64;
				int romHeight = 40;
				// int romWidth = 8160;
				// int romHeight = 1904;
				
				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2(((Main.LocalPlayer.position.X / 16) + 2) + romWidth, Main.LocalPlayer.position.Y / 16); // Offset 2 right.
				Vector2 wirePosition = playerPosition;
				
				int lineOffset = 1;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.
					
					// Define variables based on the list of words.
					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// Check if to use encoding.
								var bitFormat = ConvertXORArray(Line);

								//System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
								// Place the wires based on a status serial format - XOR ROM.
								//
								// Go through each bit within the returned binary.
								foreach (char bit in bitFormat) // For xor ConvertXORArray(arrayFourth)
								{
									// If bit is a 1 then place wire.
                                    if (bit.ToString() == "0")
                                    {
                                        // Place on lamp.
										Main.tile[(int)wirePosition.X, (int)wirePosition.Y].type = 419;
							            Main.tile[(int)wirePosition.X, (int)wirePosition.Y].frameX = 18;
                                    }
									else if (bit.ToString() == "1")
                                    {
                                        // Place faulty lamp.
										Main.tile[(int)wirePosition.X, (int)wirePosition.Y].type = 419;
							            Main.tile[(int)wirePosition.X, (int)wirePosition.Y].frameX = 36;
                                    }

									wirePosition.X--;
								}
								
								// Add lineoffset.
								lineOffset++;

								// Continue to go down.
								//
								// Reset the X position.
								wirePosition.X = playerPosition.X;
								
								// Progress position down.
                                if (lineOffset == 3)
					            {
						            wirePosition.Y += 3f;
						            lineOffset = 1;
					            }
								else
								{
									wirePosition.Y += 1f;
								}
								if (wirePosition.Y >= Main.LocalPlayer.position.Y / 16f + (float)romHeight)
								{
									wirePosition = playerPosition;
									wirePosition.Y += 2f;
									lineOffset = 1;
								}
							}

							// Display Console
							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}

			// ###################################################################################
			if (message.Text.Contains("/delrom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = 64;
				int romHeight = 40;
				// int romWidth = 8160;
				// int romHeight = 1904;
				
				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2(((Main.LocalPlayer.position.X / 16) + 2) + romWidth, Main.LocalPlayer.position.Y / 16); // Offset 2 right.
				Vector2 wirePosition = playerPosition;
				
				int lineOffset = 1;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.
					
					// Define variables based on the list of words.
					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// Check if to use encoding.
								// var bitFormat = ConvertXORArray(Line);

								//System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
								// Place the wires based on a status serial format - XOR ROM.
								//
								// Go through each bit within the returned binary.
								foreach (char bit in Line) // For xor ConvertXORArray(arrayFourth)
								{
									// Place on lamp.
									Main.tile[(int)wirePosition.X, (int)wirePosition.Y].type = 419;
							        Main.tile[(int)wirePosition.X, (int)wirePosition.Y].frameX = 18;

									wirePosition.X--;
								}
								
								// Add lineoffset.
								lineOffset++;

								// Continue to go down.
								//
								// Reset the X position.
								wirePosition.X = playerPosition.X;
								
								// Progress position down.
                                if (lineOffset == 3)
					            {
						            wirePosition.Y += 3f;
						            lineOffset = 1;
					            }
								else
								{
									wirePosition.Y += 1f;
								}
								if (wirePosition.Y >= Main.LocalPlayer.position.Y / 16f + (float)romHeight)
								{
									wirePosition = playerPosition;
									wirePosition.Y += 2f;
									lineOffset = 1;
								}
							}

							// Display Console
							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}
			
			// ###################################################################################
			if (message.Text.Contains("/zbit")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = 64;
				int romHeight = 40;
				// int romWidth = 8160;
				// int romHeight = 1904;
				
				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2(((Main.LocalPlayer.position.X / 16) + 2) + romWidth, Main.LocalPlayer.position.Y / 16); // Offset 2 right.
				Vector2 wirePosition = playerPosition;
				
				int lineOffset = 1;
				bool flipBit = false;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.
					
					// Define variables based on the list of words.
					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// Check if to use encoding.
								var bitFormat = ConvertXORArray(Line);

								//System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
								// Place the wires based on a status serial format - XOR ROM.
								//
								// Go through each bit within the returned binary.
								foreach (char bit in bitFormat) // For xor ConvertXORArray(arrayFourth)
								{
									// If bit is a 1 then place wire.
                                    if (!flipBit)
                                    {
                                        // Place on lamp.
										Main.tile[(int)wirePosition.X, (int)wirePosition.Y].type = 419;
							            Main.tile[(int)wirePosition.X, (int)wirePosition.Y].frameX = 0;
                                    }
									else
                                    {
                                        // Place faulty lamp.
										Main.tile[(int)wirePosition.X, (int)wirePosition.Y].type = 419;
							            Main.tile[(int)wirePosition.X, (int)wirePosition.Y].frameX = 18;
                                    }

									wirePosition.X--;
								}
								
								// Add lineoffset.
								lineOffset++;

								// Continue to go down.
								//
								// Reset the X position.
								wirePosition.X = playerPosition.X;
								
								// Progress position down.
                                if (lineOffset == 3)
					            {
						            wirePosition.Y += 3f;
						            lineOffset = 1;
					            }
								else
								{
									wirePosition.Y += 1f;
								}
								if (wirePosition.Y >= Main.LocalPlayer.position.Y / 16f + (float)romHeight)
								{
									wirePosition = playerPosition;
									wirePosition.Y += 2f;
									lineOffset = 1;
									flipBit = true;
								}
							}

							// Display Console
							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}
			
			// ###################################################################################
			IChatCommand chatCommand;
			if (this._commands.TryGetValue(message.CommandId, out chatCommand))
			{
				chatCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
				return;
			}

			if (this._defaultCommand != null)
			{
				this._defaultCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
			}
		}

		// Update the richtextbox.
		public static String ConvertXORArray(string value)
		{
			// Go through each char within string.
			string finalString = "";
			int previousNumber = 1;
			
			foreach (char c in value)
			{
				// Current number within string.
				int currentNumber = int.Parse(c.ToString());
				
				// Add the current with the previous.
				int mathVar = 0;
				
				// Compare mathvar to the truth table.
				if (previousNumber == 0 && currentNumber == 0) // 0 + 0 = 0.
				{
					mathVar = 0;
				}
				else if (previousNumber == 1 && currentNumber == 0) // 1 + 0 = 1 OR 0 + 1 = 1.
				{
					mathVar = 1;
				}
				else if (previousNumber == 0 && currentNumber == 1)
				{
					mathVar = 1;
				}
				else if (previousNumber == 1 && currentNumber == 1) // 1 + 1 = 0.
				{
					mathVar = 0;
				}

				// Add value to final string.
				finalString += mathVar;
				
				// Update the previous number.
				previousNumber = currentNumber;
			}

			// Return the array.
			// return String.Join(" ", new String(finalString.Reverse().ToArray())); // Reverse array.
			// return String.Join(" ", new String(finalString.ToArray())); // Nornal array.
			return String.Join(" ", new String(finalString.ToArray().Select(c => c == '0' ? '1' : '0').ToArray())); // Inverted array.
		}
	}
}
```
</details>

![ROM1](https://github.com/RussDev7/TerrariaDigitalStorage/assets/33048298/596920d3-c687-451f-b2d8-14e2ccb34918)

## **Stacked Gate ROM (2 bits each 3 tiles)**
**NOTES:**
 - Cells can easily be tiled.
 - Data is stored vertically.
 - The most lag friendly version of ROM.

**REAL WORLD USES:**
 - [Animated Gigachad Via 2000 Terraria Pixelboxes](https://www.youtube.com/watch?v=aD0I7u_OLuE)

**SAMPLE PROGRAMMING CODE:**
<details><summary>Show Code</summary>
 
```c#
//###################################################################################
//Terraria.Chat > ChatCommandProcessor (OR CreateOutgoingMessage)
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using Microsoft.Xna.Framework;
using Terraria.Chat.Commands;
using Terraria.Localization;
using Microsoft.Xna.Framework.Graphics;
using ReLogic.Content;

namespace Terraria.Chat
{
	public partial class ChatCommandProcessor : IChatProcessor
	{
		public void ProcessIncomingMessage(ChatMessage message, int clientId)
		{
			// ###################################################################################
			if (message.Text.Contains("/arom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int nextRomDistance = 44;
				// int nextRomDistance = 1308;
				int offset = 0;
				Vector2 playerPosition = new Vector2(Main.LocalPlayer.position.X / 16f + 2f + (float)offset, Main.LocalPlayer.position.Y / 16f);
				Vector2 wirePosition = playerPosition;
				int WireColor = 1;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.
					
					// Define variables based on the list of words.
					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// string[] array2 = ChatCommandProcessor.SplitIntoEqualParts(Line, 6);
								string[] array2 = ChatCommandProcessor.SplitIntoEqualParts(Line, 1);
								string[] array = array2;
								for (int i = 0; i < array.Length; i++)
								{
									foreach (string LinePart in ChatCommandProcessor.SplitIntoEqualParts(array[i], 2))
									{
										// Check if to use encoding.
										var bitFormat = ConvertXORArray(LinePart);
										for (int k = 0; k < bitFormat.Length; k++)
										{
											if (bitFormat[k].ToString() == "1")
											{
												if (WireColor == 1)
												{
													WorldGen.PlaceWire2((int)wirePosition.X, (int)wirePosition.Y);
												}
												else if (WireColor == 2)
												{
													WorldGen.PlaceWire((int)wirePosition.X, (int)wirePosition.Y);
												}
											}

											wirePosition.Y += 3f;
										}

										wirePosition.Y = playerPosition.Y;
										WireColor = 2;
									}

									WireColor = 1;
									wirePosition.X += (float)(nextRomDistance - 1);
								}

								WireColor = 1;
								offset++;
								playerPosition = new Vector2(Main.LocalPlayer.position.X / 16f + 2f + (float)offset, Main.LocalPlayer.position.Y / 16f);
								wirePosition = playerPosition;
							}

							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}

			// ###################################################################################
			if (message.Text.Contains("/delrom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int nextRomDistance = 44;
				// int nextRomDistance = 1308;
				int offset = 0;
				Vector2 playerPosition = new Vector2(Main.LocalPlayer.position.X / 16f + 2f + (float)offset, Main.LocalPlayer.position.Y / 16f);
				Vector2 wirePosition = playerPosition;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.
					// Define variables based on the list of words.
					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// string[] array2 = ChatCommandProcessor.SplitIntoEqualParts(Line, 6);
								string[] array2 = ChatCommandProcessor.SplitIntoEqualParts(Line, 1);
								string[] array = array2;
								for (int i = 0; i < array.Length; i++)
								{
									foreach (string LinePart in ChatCommandProcessor.SplitIntoEqualParts(array[i], 2))
									{
										// Check if to use encoding.
										var bitFormat = ConvertXORArray(LinePart);
										foreach (char bit in bitFormat)
										{
											Main.tile[(int)wirePosition.X, (int)wirePosition.Y].wire(false);
											Main.tile[(int)wirePosition.X, (int)wirePosition.Y].wire2(false);
											wirePosition.Y += 3f;
										}

										wirePosition.Y = playerPosition.Y;
									}

									wirePosition.X += (float)(nextRomDistance - 1);
								}

								offset++;
								playerPosition = new Vector2(Main.LocalPlayer.position.X / 16f + 2f + (float)offset, Main.LocalPlayer.position.Y / 16f);
								wirePosition = playerPosition;
							}

							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}

			// ###################################################################################
			IChatCommand chatCommand;
			if (this._commands.TryGetValue(message.CommandId, out chatCommand))
			{
				chatCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
				return;
			}

			if (this._defaultCommand != null)
			{
				this._defaultCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
			}
		}

		// Update the richtextbox.
		public static String ConvertXORArray(string value)
		{
			// Go through each char within string.
			string finalString = "";
			int previousNumber = 1;
			foreach (char c in value)
			{
				// Current number within string.
				int currentNumber = int.Parse(c.ToString());
				// Add the current with the previous.
				int mathVar = 0;
				// Compare mathvar to the truth table.
				if (previousNumber == 0 && currentNumber == 0) // 0 + 0 = 0.
				{
					mathVar = 0;
				}
				else if (previousNumber == 1 && currentNumber == 0) // 1 + 0 = 1 OR 0 + 1 = 1.
				{
					mathVar = 1;
				}
				else if (previousNumber == 0 && currentNumber == 1)
				{
					mathVar = 1;
				}
				else if (previousNumber == 1 && currentNumber == 1) // 1 + 1 = 0.
				{
					mathVar = 0;
				}

				// Add value to final string.
				finalString += mathVar;
				// Update the previous number.
				previousNumber = currentNumber;
			}

			// Return the array.
			// return String.Join(" ", new String(finalString.Reverse().ToArray())); // Reverse array.
			return String.Join(" ", new String(finalString.ToArray())); // Nornal array.
		// return String.Join(" ", new String(finalString.ToArray().Select(c => c == '0' ? '1' : '0').ToArray())); // Inverted array.
		}

		// Split string into equal parts.
		public static String[] SplitIntoEqualParts(string str, int lengh)
		{
			int temp = 0, chars = str.Length / lengh;
			String[] equalStr = new String[lengh];
			// Check whether a string can be divided into n equal parts.
			if (str.Length % lengh != 0)
			{
				Console.WriteLine("ERROR: String cannot be divided into " + lengh + " equal parts.");
				return new String[]{};
			}
			else
			{
				for (int i = 0; i < str.Length; i = i + chars)
				{
					// Dividing string in n equal part using substring().
					string part = str.Substring(i, chars);
					equalStr[temp] = part;
					temp++;
				}

				return equalStr;
			}
		}
	}
}
```
</details>

![ROM2](https://github.com/RussDev7/TerrariaDigitalStorage/assets/33048298/7ef192c2-6c3e-4a11-8226-b07321c82654)

## **Stacked Lamp Serial ROM (4 bits each 3 tiles)**
**NOTES:**
 - Full serial-based ROM.
 - Data is stored horizontally.
 - Densest version of ROM.

**REAL WORLD USES:**
 - [v2: Terraria But Its Animated Shrek](https://www.youtube.com/watch?v=EVP2zqgrtzg)

**SAMPLE PROGRAMMING CODE:**
<details><summary>Show Code</summary>
 
```c#
//###################################################################################
//Terraria.Chat > ChatCommandProcessor (OR CreateOutgoingMessage)
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using Microsoft.Xna.Framework;
using Terraria.Chat.Commands;
using Terraria.Localization;
using Microsoft.Xna.Framework.Graphics;
using ReLogic.Content;

namespace Terraria.Chat
{
	public partial class ChatCommandProcessor : IChatProcessor
	{
		public void ProcessIncomingMessage(ChatMessage message, int clientId)
		{
			// ###################################################################################
			if (message.Text.Contains("/arom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = 64;
				// int romWidth = 8160;
				// int romHeight = 1904;
				
				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2(((Main.LocalPlayer.position.X / 16) + 5) + romWidth, Main.LocalPlayer.position.Y / 16); // Offset 5 right.
				Vector2 wirePosition = playerPosition;
				
				int lineOffset = 1;
				int WireColor = 1;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.
					
					// Define variables based on the list of words.
					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// Check if to use encoding.
								var bitFormat = ConvertXORArray(Line);

								//System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
								// Place the wires based on a status serial format - XOR ROM.
								//
								// Go through each bit within the returned binary.
								foreach (char bit in bitFormat) // For xor ConvertXORArray(arrayFourth)
								{
									// If bit is a 1 then place wire.
                                    if (bit.ToString() == "1")
                                    {
                                        // Get the wire color.
                                        if (WireColor == 1) // Blue first 1-14, red second 15,28.
                                        {
                                            // Place red wire.
                                            WorldGen.PlaceWire((int)wirePosition.X, (int)wirePosition.Y);
                                        }
                                        else if (WireColor == 2)
                                        {
                                            // Place blue wire.
                                            WorldGen.PlaceWire2((int)wirePosition.X, (int)wirePosition.Y);
                                        }
										else if (WireColor == 3)
                                        {
                                            // Place green wire.
                                            WorldGen.PlaceWire3((int)wirePosition.X, (int)wirePosition.Y);
                                        }
										else if (WireColor == 4)
                                        {
                                            // Place yellow wire.
                                            WorldGen.PlaceWire4((int)wirePosition.X, (int)wirePosition.Y);
                                        }
                                    }

									// Move the postion 1 right for the next byte.
									wirePosition.X--;
								}

								// Continue to go down.
								//
								// Reset the X position.
								wirePosition.X = playerPosition.X;

								// Progress wire color.
                                if (WireColor == 4)
                                {
                                    // Reset wirecolor back to 1.
                                    WireColor = 1;
									
                                    // Progress position down.
                                    wirePosition.Y += 3;
                                }
                                else
                                {
                                    WireColor++;
                                }
							}

							// Display Console
							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}

			// ###################################################################################
			if (message.Text.Contains("/delrom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = 64;
				// int romWidth = 8160;
				// int romHeight = 1904;
				
				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2(((Main.LocalPlayer.position.X / 16) + 5) + romWidth, Main.LocalPlayer.position.Y / 16); // Offset 5 right.
				Vector2 wirePosition = playerPosition;
				
				int lineOffset = 1;
				int WireColor = 1;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.
					
					// Define variables based on the list of words.
					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// Check if to use encoding.
								var bitFormat = ConvertXORArray(Line);

								//System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
								// Place the wires based on a status serial format - XOR ROM.
								//
								// Go through each bit within the returned binary.
								foreach (char bit in bitFormat) // For xor ConvertXORArray(arrayFourth)
								{
									// Remove wire colors.
                                    WorldGen.KillWire((int)wirePosition.X, (int)wirePosition.Y);
									WorldGen.KillWire2((int)wirePosition.X, (int)wirePosition.Y);
									WorldGen.KillWire3((int)wirePosition.X, (int)wirePosition.Y);
									WorldGen.KillWire4((int)wirePosition.X, (int)wirePosition.Y);

									// Move the postion 1 right for the next byte.
									wirePosition.X--;
								}

								// Continue to go down.
								//
								// Reset the X position.
								wirePosition.X = playerPosition.X;

								// Progress wire color.
                                if (WireColor == 4)
                                {
                                    // Reset wirecolor back to 1.
                                    WireColor = 1;
									
                                    // Progress position down.
                                    wirePosition.Y += 3;
                                }
                                else
                                {
                                    WireColor++;
                                }
							}

							// Display Console
							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}
			
			// ###################################################################################
			IChatCommand chatCommand;
			if (this._commands.TryGetValue(message.CommandId, out chatCommand))
			{
				chatCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
				return;
			}

			if (this._defaultCommand != null)
			{
				this._defaultCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
			}
		}

		// Update the richtextbox.
		public static String ConvertXORArray(string value)
		{
			// Go through each char within string.
			string finalString = "";
			int previousNumber = 1;
			foreach (char c in value)
			{
				// Current number within string.
				int currentNumber = int.Parse(c.ToString());
				// Add the current with the previous.
				int mathVar = 0;
				// Compare mathvar to the truth table.
				if (previousNumber == 0 && currentNumber == 0) // 0 + 0 = 0.
				{
					mathVar = 0;
				}
				else if (previousNumber == 1 && currentNumber == 0) // 1 + 0 = 1 OR 0 + 1 = 1.
				{
					mathVar = 1;
				}
				else if (previousNumber == 0 && currentNumber == 1)
				{
					mathVar = 1;
				}
				else if (previousNumber == 1 && currentNumber == 1) // 1 + 1 = 0.
				{
					mathVar = 0;
				}

				// Add value to final string.
				finalString += mathVar;
				// Update the previous number.
				previousNumber = currentNumber;
			}

			// Return the array.
			// return String.Join(" ", new String(finalString.Reverse().ToArray())); // Reverse array.
			// return String.Join(" ", new String(finalString.ToArray())); // Nornal array.
			return String.Join(" ", new String(finalString.ToArray().Select(c => c == '0' ? '1' : '0').ToArray())); // Inverted array.
		}
	}
}
```
</details>

![ROM3](https://github.com/RussDev7/TerrariaDigitalStorage/assets/33048298/c62619cb-1455-4f3a-821b-ed803514a75c)

## **Stacked Lamp Parallel ROM (4 bits each 3 tiles)**
**NOTES:**
 - Parallel variation of the serial-based ROM.
 - Data is stored horizontally.
 - Optimized lag performance over serial.
 - Densest version of ROM.

**REAL WORLD USES:**
 - [v3: Terraria But Its Animated Shrek](https://www.youtube.com/watch?v=EVP2zqgrtzg)

**SAMPLE PROGRAMMING CODE:**
<details><summary>Show Code</summary>
 
```c#
//###################################################################################
//Terraria.Chat > ChatCommandProcessor (OR CreateOutgoingMessage)
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using Microsoft.Xna.Framework;
using Terraria.Chat.Commands;
using Terraria.Localization;
using Microsoft.Xna.Framework.Graphics;
using ReLogic.Content;

namespace Terraria.Chat
{
	public partial class ChatCommandProcessor : IChatProcessor
	{
		public void ProcessIncomingMessage(ChatMessage message, int clientId)
		{
			// ###################################################################################
			if (message.Text.Contains("/arom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = 64;
				int romHeight = 29;
				// int romWidth = 8160;
				// int romHeight = 2249;

				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2((Main.LocalPlayer.position.X / 16), (Main.LocalPlayer.position.Y / 16) + 5); // Offset 5 down.
				Vector2 wirePosition = playerPosition;
				int lineOffset = 1;
				int WireColor = 1;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.

					// Define variables based on the list of words.

					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Create a string of 0s width long.
							char[] previousLine = new char[romWidth];
							for (int i = 0; i < romWidth; i++)
							{
								previousLine[i] = '0';
							}

							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// Check if to use encoding.
								string bitFormat = "";
								for (int i = 0; i < romWidth; i++)
								{
									bitFormat += (int.Parse(previousLine[i].ToString()) ^ int.Parse(Line[i].ToString())).ToString();
									previousLine[i] = Line[i];
								}

								// System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
								// Place the wires based on a status serial format - XOR ROM.
								//
								// Go through each bit within the returned binary.
								foreach (char bit in bitFormat) // For xor ConvertXORArray(arrayFourth)
								{
									// If bit is a 1 then place wire.
									if (bit.ToString() == "1")
									{
										// Get the wire color.
										if (WireColor == 1) // Blue first 1-14, red second 15,28.
										{
											// Place red wire.
											WorldGen.PlaceWire((int)wirePosition.X, (int)wirePosition.Y);
										}
										else if (WireColor == 2)
										{
											// Place blue wire.
											WorldGen.PlaceWire2((int)wirePosition.X, (int)wirePosition.Y);
										}
										else if (WireColor == 3)
										{
											// Place green wire.
											WorldGen.PlaceWire3((int)wirePosition.X, (int)wirePosition.Y);
										}
										else if (WireColor == 4)
										{
											// Place yellow wire.
											WorldGen.PlaceWire4((int)wirePosition.X, (int)wirePosition.Y);
										}
									}

									// Move the postion 1 right for the next byte.
									wirePosition.X++;
								}

								// Continue to go down.
								//
								// Reset the X position.
								wirePosition.X = playerPosition.X;

								// Progress wire color.
								if (WireColor == 4)
								{
									// Reset wirecolor back to 1.
									WireColor = 1;

									// Progress position down.
									wirePosition.Y += 3;
								}
								else
								{
									WireColor++;
								}
							}

							// Display Console
							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}

			// ###################################################################################
			if (message.Text.Contains("/delrom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = 64;
				int romHeight = 29;
				// int romWidth = 8160;
				// int romHeight = 2249;

				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2((Main.LocalPlayer.position.X / 16), (Main.LocalPlayer.position.Y / 16) + 5); // Offset 5 down.
				Vector2 wirePosition = playerPosition;
				int lineOffset = 1;
				int WireColor = 1;
				try
				{
					// Create a list with height amount of rows of width long charecters.
					var clearSchematic = new List<string>(Enumerable.Repeat("".PadLeft(romWidth, '0'), romHeight).ToList());

					// Go through each line of wordlist.
					foreach (string Line in clearSchematic)
					{
						// System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
						// Place the wires based on a status serial format - XOR ROM.
						//
						// Go through each bit within the returned binary.
						foreach (char bit in Line) // For xor ConvertXORArray(arrayFourth)
						{
							// Remove wire colors.
							WorldGen.KillWire((int)wirePosition.X, (int)wirePosition.Y);
							WorldGen.KillWire2((int)wirePosition.X, (int)wirePosition.Y);
							WorldGen.KillWire3((int)wirePosition.X, (int)wirePosition.Y);
							WorldGen.KillWire4((int)wirePosition.X, (int)wirePosition.Y);
							// Move the postion 1 right for the next byte.
							wirePosition.X++;
						}

						// Continue to go down.
						//
						// Reset the X position.
						wirePosition.X = playerPosition.X;

						// Progress wire color.
						if (WireColor == 4)
						{
							// Reset wirecolor back to 1.
							WireColor = 1;

							// Progress position down.
							wirePosition.Y += 3;
						}
						else
						{
							WireColor++;
						}
					}

					// Display Console
					Console.WriteLine(string.Concat(new string[]{"The ROM has been wiped! Area cleaned: " + romWidth + "x" + romHeight}));
					Main.NewTextMultiline(string.Concat(new string[]{"The ROM has been wiped! Area cleaned: " + romWidth + "x" + romHeight}), false, Color.Green, -1);

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /delrom");
					Main.NewTextMultiline("ERROR: Command Usage - /delrom", false, Color.Red, -1);
				}

				return;
			}

			// ###################################################################################
			IChatCommand chatCommand;
			if (this._commands.TryGetValue(message.CommandId, out chatCommand))
			{
				chatCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
				return;
			}

			if (this._defaultCommand != null)
			{
				this._defaultCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
			}
		}
	}
}
```
</details>

![ROM4](https://github.com/RussDev7/TerrariaDigitalStorage/assets/33048298/fbc6edb4-6493-481f-90db-829f45200f79)

## **Duel Cell Stacked Lamp Parallel ROM (4 bits each 3 tiles)**
**NOTES:**
 - Parallel variation of the serial-based ROM.
 - Data is stored horizontally.
 - Optimized lag performance over serial.
 - Densest version of ROM.
 - Cells where split allow for super large screen support.

**REAL WORLD USES:**
 - [The largest possible pixelbox display in Terraria playing badapple!!](https://www.youtube.com/watch?v=bE96Vxmg8Xw)

**SAMPLE PROGRAMMING CODE:**
<details><summary>Show Code</summary>
 
```c#
//###################################################################################
//Terraria.Chat > ChatCommandProcessor (OR CreateOutgoingMessage)
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using Microsoft.Xna.Framework;
using Terraria.Chat.Commands;
using Terraria.Localization;
using Microsoft.Xna.Framework.Graphics;
using ReLogic.Content;

namespace Terraria.Chat
{
	public partial class ChatCommandProcessor : IChatProcessor
	{
		public void ProcessIncomingMessage(ChatMessage message, int clientId)
		{
			// ###################################################################################
			if (message.Text.Contains("/arom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = (32) * 2; // 64
				int romHeight = (60);
				// int romWidth = (6624) * 2; // 13248
				// int romHeight = (2100);

				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2((Main.LocalPlayer.position.X / 16), (Main.LocalPlayer.position.Y / 16) + 5); // Offset 5 down.
				Vector2 wirePosition = playerPosition;
				int lineOffset = 1;
				int WireColor = 1;
				try
				{
					// Create a new list of words based on a sentences spaces.
					List<string> wordList = Message.TrimStart(new char[]{' '}).Split(new char[]{' '}).ToList<string>();
					wordList.RemoveAt(0); // Remove the first word -> /schem.

					// Define variables based on the list of words.

					foreach (string OutString in string.Join(" ", wordList.ToArray()).Split(new char[]{' '}))
					{
						if (RunBy == 0) // Define the first variable.
						{
							SchemName = OutString;
							RunBy++;
						}
					}

					// Load Schem
					if (SchemName == "")
					{
						Console.WriteLine("ERROR: Type a schematic name!");
						Main.NewTextMultiline("ERROR: Type a schematic name!", false, Color.Red, -1);
						return;
					}
					else
					{
						try
						{
							// Create a string of 0s width long.
							char[] previousLine = new char[romWidth];
							for (int i = 0; i < romWidth; i++)
							{
								previousLine[i] = '0';
							}

							// Go through each line of wordlist.
							foreach (string Line in File.ReadLines(@"C:\Program Files (x86)\Steam\steamapps\common\Terraria\#romupload\" + SchemName + ".txt"))
							{
								// Check if to use encoding.
								string bitFormat = "";
								for (int i = 0; i < romWidth; i++)
								{
									bitFormat += (int.Parse(previousLine[i].ToString()) ^ int.Parse(Line[i].ToString())).ToString();
									previousLine[i] = Line[i];
								}

								// Split the encoding in half and distribute bits between both ROMs.
								var splitHalfHalfs = new[]{bitFormat.Substring(0, (int)(bitFormat.Length / 2)), bitFormat.Substring((int)(bitFormat.Length / 2), bitFormat.Length - (int)(bitFormat.Length / 2))};

								// System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
								// Place the wires based on a status serial format - XOR ROM.
								//
								// Go through each bit within the returned binary.
								// Copy wires first half.
								foreach (char bit in splitHalfHalfs[0]) // For xor ConvertXORArray(arrayFourth)
								{
									// If bit is a 1 then place wire.
									if (bit.ToString() == "1")
									{
										// Get the wire color.
										if (WireColor == 1) // Blue first 1-14, red second 15,28.
										{
											// Place red wire.
											WorldGen.PlaceWire((int)wirePosition.X, (int)wirePosition.Y);
										}
										else if (WireColor == 2)
										{
											// Place blue wire.
											WorldGen.PlaceWire2((int)wirePosition.X, (int)wirePosition.Y);
										}
										else if (WireColor == 3)
										{
											// Place green wire.
											WorldGen.PlaceWire3((int)wirePosition.X, (int)wirePosition.Y);
										}
										else if (WireColor == 4)
										{
											// Place yellow wire.
											WorldGen.PlaceWire4((int)wirePosition.X, (int)wirePosition.Y);
										}
									}

									// Move the postion 1 right for the next byte.
									wirePosition.X++;
								}

								// Reset wire position.
								wirePosition.X = playerPosition.X;

								// Copy wires second half.
								foreach (char bit in splitHalfHalfs[1]) // For xor ConvertXORArray(arrayFourth)
								{
									// If bit is a 1 then place wire.
									if (bit.ToString() == "1")
									{
										// Get the wire color.
										if (WireColor == 1) // Blue first 1-14, red second 15,28.
										{
											// Place red wire.
											// WorldGen.PlaceWire((int)wirePosition.X, (int)wirePosition.Y + (romHeight / 2) + 78);
											WorldGen.PlaceWire((int)wirePosition.X, (int)wirePosition.Y + (romHeight / 2) + 10);
										}
										else if (WireColor == 2)
										{
											// Place blue wire.
											// WorldGen.PlaceWire2((int)wirePosition.X, (int)wirePosition.Y + (romHeight / 2) + 78);
											WorldGen.PlaceWire2((int)wirePosition.X, (int)wirePosition.Y + (romHeight / 2) + 10);
										}
										else if (WireColor == 3)
										{
											// Place green wire.
											// WorldGen.PlaceWire3((int)wirePosition.X, (int)wirePosition.Y + (romHeight / 2) + 78);
											WorldGen.PlaceWire3((int)wirePosition.X, (int)wirePosition.Y + (romHeight / 2) + 10);
										}
										else if (WireColor == 4)
										{
											// Place yellow wire.
											// WorldGen.PlaceWire4((int)wirePosition.X, (int)wirePosition.Y + (romHeight / 2) + 78);
											WorldGen.PlaceWire4((int)wirePosition.X, (int)wirePosition.Y + (romHeight / 2) + 10);
										}
									}

									// Move the postion 1 right for the next byte.
									wirePosition.X++;
								}

								// Continue to go down.
								//
								// Reset the X position.
								wirePosition.X = playerPosition.X;

								// Progress wire color.
								if (WireColor == 4)
								{
									// Reset wirecolor back to 1.
									WireColor = 1;

									// Progress position down.
									wirePosition.Y += 3;
								}
								else
								{
									WireColor++;
								}
							}

							// Display Console
							Console.WriteLine(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}));
							Main.NewTextMultiline(string.Concat(new string[]{"Schematic: ", SchemName.ToString(), " has loaded successfully!"}), false, Color.Green, -1);
						}
						catch (Exception)
						{
							Console.WriteLine("Schematic: " + SchemName.ToString() + " was not found!");
							Main.NewTextMultiline("Schematic: " + SchemName.ToString() + " was not found!", false, Color.Red, -1);
						}
					}

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /arom [schem]");
					Main.NewTextMultiline("ERROR: Command Usage - /arom [schem]", false, Color.Red, -1);
				}

				return;
			}

			// ###################################################################################
			if (message.Text.Contains("/delrom")) // Animation rom.
			{
				string SchemName = "";
				int RunBy = 0;
				string Message = message.Text;
				int romWidth = (32);
				int romHeight = ((60) / 2) / 3; // 10
				// int romWidth = (6624);
				// int romHeight = ((2100) / 2) / 3; // 350

				// We are starting from the right to left so add the width.
				Vector2 playerPosition = new Vector2((Main.LocalPlayer.position.X / 16), (Main.LocalPlayer.position.Y / 16) + 5); // Offset 5 down.
				Vector2 wirePosition = playerPosition;
				int lineOffset = 0;
				int WireColor = 1;
				try
				{
					// Create a list with height amount of rows of width long charecters.
					var clearSchematic = new List<string>(Enumerable.Repeat("".PadLeft(romWidth, '0'), romHeight).ToList());

					// Go through each line of wordlist.
					foreach (string Line in clearSchematic)
					{
						// System.Windows.Forms.MessageBox.Show(ConvertArrayBuilder(arrayHalf));        
						// Place the wires based on a status serial format - XOR ROM.
						//
						// Go through each bit within the returned binary.
					    foreach (char bit in Line) // For xor ConvertXORArray(arrayFourth)
						{
							// Remove wire colors.
							WorldGen.KillWire((int)wirePosition.X, (int)wirePosition.Y);
							WorldGen.KillWire2((int)wirePosition.X, (int)wirePosition.Y);
							WorldGen.KillWire3((int)wirePosition.X, (int)wirePosition.Y);
							WorldGen.KillWire4((int)wirePosition.X, (int)wirePosition.Y);
							// Move the postion 1 right for the next byte.
							wirePosition.X++;
						}
						
						// Reset wire position.
						wirePosition.X = playerPosition.X;
								
						foreach (char bit in Line) // For xor ConvertXORArray(arrayFourth)
						{
							// Remove wire colors.
							// WorldGen.KillWire((int)wirePosition.X, ((int)playerPosition.Y + (romHeight * 3) + 31) + (int)wirePosition.Y);
							// WorldGen.KillWire2((int)wirePosition.X, ((int)playerPosition.Y + (romHeight * 3) + 31) + (int)wirePosition.Y);
							// WorldGen.KillWire3((int)wirePosition.X, ((int)playerPosition.Y + (romHeight * 3) + 31) + (int)wirePosition.Y);
							// WorldGen.KillWire4((int)wirePosition.X, ((int)playerPosition.Y + (romHeight * 3) + 31) + (int)wirePosition.Y);
							
							WorldGen.KillWire((int)wirePosition.X, (int)wirePosition.Y + (romHeight * 3) + 10);
							WorldGen.KillWire2((int)wirePosition.X, (int)wirePosition.Y + (romHeight * 3) + 10);
							WorldGen.KillWire3((int)wirePosition.X, (int)wirePosition.Y + (romHeight * 3) + 10);
							WorldGen.KillWire4((int)wirePosition.X, (int)wirePosition.Y + (romHeight * 3) + 10);
							
							// Move the postion 1 right for the next byte.
							wirePosition.X++;
						}
						
						// Continue to go down.
						//
						// Reset the X position.
						wirePosition.X = playerPosition.X;
						
						// Move three down.
						wirePosition.Y += 3;
					}

					// Display Console
					Console.WriteLine(string.Concat(new string[]{"The ROM has been wiped! Area cleaned: " + romWidth + "x" + ((romHeight * 3) * 2)}));
					Main.NewTextMultiline(string.Concat(new string[]{"The ROM has been wiped! Area cleaned: " + romWidth + "x" + ((romHeight * 3) * 2)}), false, Color.Green, -1);

					// Command Finished
					return;
				}
				catch (Exception)
				{
					Console.WriteLine("ERROR: Command Usage - /delrom");
					Main.NewTextMultiline("ERROR: Command Usage - /delrom", false, Color.Red, -1);
				}

				return;
			}

			// ###################################################################################
			IChatCommand chatCommand;
			if (this._commands.TryGetValue(message.CommandId, out chatCommand))
			{
				chatCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
				return;
			}

			if (this._defaultCommand != null)
			{
				this._defaultCommand.ProcessIncomingMessage(message.Text, (byte)clientId);
				message.Consume();
			}
		}
	}
}
```
</details>

![ROM5](https://github.com/RussDev7/TerrariaDigitalStorage/assets/33048298/07ed023c-4d58-48f1-8a55-5d3ba1e9496c)

## **Accelerating The Wiring**

Due to the way Terraria logic system is, larger wiring builds will freeze your game each time you power them up. To fix this, we can use a mod called the [Terraria Circuit Preprocessing Accelerator](https://github.com/RussDev7/TerrariaCircuitPreprocessingAccelerator-1.4.4.9) to pre-process the wiring and prevent lagging while still maintaining the vanilla logic system.
