---
layout: post
title: Java wrapper for Windows Biometric Framework 
meta: WBF
source: http://www.ebook.com/about
category: blog
tags:
- Java
- JNI
- C++
- Windows Biometric Framework
description: Wrapping WBF with Java wrapper to enable biometric fingerprint scanner to communicate with Windows operating system using Java language.
---

In today's cyberspace, its a fact to say credentials theft is the central technique of all cyber hacking. 
People use passwords/PIN Codes which are easy to recall, so they are easily cracked and soon or later misplaced/forgotten. Hackers love it and knows that is a weakness humans have ignored for far too long. This article is not about password but I hate passwords and I cannot stress enough they are so outdated. That is why I decided to write about an alternative that is no-brainer its most appropriate way to manage authentication of users. Its non other than using biometrics, well, its not new idea. 
But we need to, if not must use individual unique characteristics to identify people just the way nature does it. I mean, I can't confuse my girlfriend with another girl because my brain can distinguish physical characteristics whether its face, shape, voice, e.t.c. Computer can do much better since they are keener and have all the time to calculate. They can be used to uniquely identify and compare people fingerprints, face, iris, heartbeat patterns. Luckily computer scientist have been on
their heels to make use of biometrics as our second nature while interacting with computers.

### Windows Biometric Framework(WBF) ###
Windows Biometric Framework(WBF) is Microsoft Windows OS component introduced in Windows 7. Since we are programmer and like to explore lets take a look at how WBF can be used to identify users using their fingerprints. You will require a fingerprint sensor to capture biometric information, Windows 7 or later, Netbeans IDE, Visual Studio and Java and C++ environment. WBF is implemented in C++ and since we love Java, shall use JNI to interface with native code. This is not JNI tutorial but if you are newbie you can check out <this> on how to implement JNI. 

### Create Java application ###
The Java application will act as the graphical interface to control and display events happening on WBF background service. Lets create a new JavaFX application called ***WinBiometric*** using Netbeans IDE and create application class called ***com.github.pistolla.WinBiometric***. We create the FXML file and name it ***FingerPrintIdentifier.fxml***, ensure to create a controller and tuck it into a package namespace ***com.github.pistolla.FingerPrintIdentifier*** or any thing similar.

![Figure 1]({{site.baseurl}}/img/content/java-wrapper-fig-1.jpg)

You can now add visual component as illustrated below. You can download the source code of this project on [github](http://github.com/pistolla/WinBiometric). This components will allow us to send commands to the WBF native interface via JNI interface.

![Figure 2]({{site.baseurl}}/img/content/java-wrapper-fig-2.jpg)

To invoke the native code on Java application we need add the following methods with native keyword in controller ***FingerPrintIdentifier.java***. I will explain later each method when we begin implementing the C++ code that is called when each method is invoked.

<code>

	private native void getAvailableFingerPrintSensors();
    
    private native void locateOrQueryUnit(int unitId);
    
    private native void enrollNewUser();
    
    private native void captureUserFingerPrint();
    
    private native void getFingerPrintAsImage();
    
    private native void commitUser();
    
    private native void identifyUser();
    
    private native void setSensorMessages(String[] messages);
    
    private native void setBiometricUnits(int[] units);
    
    private native void setBitmapImage(String path);
</code>
Since now we have our Java application calling native methods, we need to implement the interface that communicates to C++ environment. This is done using javah utility command. First we need to compile the Java application into Java byte code. Click on build option on Netbeans IDE to compile he project. This is important when invoking javah utility command on class file to create a header file. Open the command prompt as administrator and change directory to the base folder of your project. 

![Figure 3]({{site.baseurl}}/img/content/java-wrapper-fig-3.jpg)

Run the javah utility in the command prompt to generate a header file that can be implemented using C/C++ language. Do not edit the generated file.

<code>

	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_getAvailableFingerPrintSensors(JNIEnv *, jobject);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_locateOrQueryUnit(JNIEnv *, jobject, jint);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_enrollNewUser(JNIEnv *, jobject);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_captureUserFingerPrint(JNIEnv *, jobject);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_getFingerPrintAsImage(JNIEnv *, jobject);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_commitUser(JNIEnv *, jobject);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_identifyUser(JNIEnv *, jobject);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_setSensorMessages(JNIEnv *, jobject, jobjectArray);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_setBiometricUnits(JNIEnv *, jobject, jintArray);
	JNIEXPORT void JNICALL Java_com_github_pistolla_FingerPrintIdentifier_setBitmapImage(JNIEnv *, jobject, jstring);
</code>
We can now leave the Java application for awhile and implement the C++ code that get called via native methods. This code perform will wrap around the WBF code to perform all logic of interacting with WBF library.

### Windows Biometric Framework API ###
Essentially, Windows Biometric Framework API exposes that enables developer to create application that can identify or verify users, locate or query biometric devices and manage sessions or monitor events. We want to create an application that enrolls some users using their fingerprint, store these details in a database, then later verify the users if they are already enrolled by swiping their fingerprint on scanner.
WBF provides 3 types of sensor pools, which includes: system, private and unassigned pools. According to <documentation> sensor pools refers to biometric units which... . We are interested with private sensor pool since we will enroll users who don't have system accounts to use computer thus our sensor pool is exclusively reserved for our application use only. Hence we are creating a client application that uses private pools. We also need a plan of actions before we start consuming the API to make user experience seamless.
  
#### WBF API endpoints ####
The WBF API exposes this important endpoints to perform required actions. The endpoints we will likely use include:-

1. WinBioEnumBiometricUnits - To enumerate all biometric units available to our sensor pool.
2. WinBioOpenSession - To open a biometric session and connect to sensor pool.
3. WinBioLocateSensor - To locate and query a biometric unit.
4. WinBioEnrollBegin - To start enrolment sequence
5. WinBioEnrollCapture - To Process multiple finger swipes on the sensor
6. WinBioEnrollCommit - To save the sample/template in the store
The documentation has included extra explanation and samples to show 

#### Consuming WBF API and wrapping using native code(C++)####
We can now implement methods with native keyword that we created earlier and generated a header file. Create a C++ project using Visual Studio or you favorite C++ environment, and name it WBFWrapper. Ensure the project is a console windows project and it compiles the code into a dll as shown below. Copy the generated header file into projects header files and compile the project.

**1**. **getAvailableFingerPrintSensors()** - request for all biometric devices available in sensor pool. if the returned array is not empty we add into a selectable combo box. When a biometric unit is selected call native method to locate and query a unit.

<code>

	void getAvailableUnits()
	{
		WINBIO_UNIT_ID unitIdArray[1] = {};
		SIZE_T unitIdCount = ARRAYSIZE(unitIdArray);
	
		WINBIO_UNIT_SCHEMA *unitSchemaArray = NULL;
		SIZE_T unitSchemaCount = 0;
		HRESULT hr = WinBioEnumBiometricUnits(
			WINBIO_TYPE_FINGERPRINT,
			&unitSchemaArray,
			&unitSchemaCount
			);
		if (SUCCEEDED(hr))
		{
			displayUnits(
				_T("Choose a sensor for the private pool"),
				unitSchemaArray,
				unitSchemaCount
				);
			
			WinBioFree(unitSchemaArray);
			unitSchemaArray = NULL;
			unitSchemaCount = 0;
		}
	}
</code> 
**2**. **createPrivatePool()** - We verify our database has not already been installed. A database has a GUID to uniquely identify our private pool. The GUID is generated and stored privately in our code. We use the GUID to install a new Database using the WBF API.
<code>

	void onInstall(__in SIZE_T selectedUnit)
	{
		WINBIO_UUID databaseId = PRIVATE_POOL_DATABASE_ID;
		WINBIO_UUID dataFormat = {};
	
		if (isDatabaseInstalled(&databaseId, &dataFormat))
		{
			_tprintf(_T("*** Error - private database already installed.\n"));
			return;
		}
	
		WINBIO_UNIT_SCHEMA *unitArray = NULL;
		SIZE_T unitCount = 0;
		HRESULT hr = WinBioEnumBiometricUnits(
			WINBIO_TYPE_FINGERPRINT,
			&unitArray,
			&unitCount
			);
		if (SUCCEEDED(hr))
		{
			BioHelper::POOL_CONFIGURATION derivedConfig = {};
			hr = BioHelper::CreateCompatibleConfiguration(
				&unitArray[selectedUnit],
				&derivedConfig
				);
			if (SUCCEEDED(hr))
			{
				WINBIO_STORAGE_SCHEMA storageSchema = {};
				storageSchema.DatabaseId = PRIVATE_POOL_DATABASE_ID;
				storageSchema.DataFormat = derivedConfig.DataFormat;
				storageSchema.Attributes = derivedConfig.DatabaseAttributes;
				hr = BioHelper::RegisterDatabase(&storageSchema);
			}
	
			WinBioFree(unitArray);
			unitArray = NULL;
			unitCount = 0;
		}
		if (SUCCEEDED(hr))
		{
			_tprintf(_T("- Private database installed.\n"));
		}
		else
		{
			LPTSTR errorMessage = BioHelper::ConvertErrorCodeToString(hr);
			_tprintf(_T("*** Error - %s\n"), errorMessage);
			LocalFree(errorMessage);
			errorMessage = NULL;
		}
	}
</code>

**3.** **locateOrQueryUnit(unitId)** - Query and locate if a biometric unit is available. The method return 1 if its available else 0. The accepts uniId as parameter. 

<code>

	void locateOrQuery(SIZE_T selectedUnit)
	{
		WINBIO_UNIT_ID unitIdArray[1] = {};
		SIZE_T unitIdCount = ARRAYSIZE(unitIdArray);
	
		WINBIO_UNIT_SCHEMA *unitSchemaArray = NULL;
		WINBIO_UNIT_SCHEMA *selectedunitSchema = NULL;
		SIZE_T unitSchemaCount = 0;
		HRESULT hr = WinBioEnumBiometricUnits(
			WINBIO_TYPE_FINGERPRINT,
			&unitSchemaArray,
			&unitSchemaCount
			);
		if (SUCCEEDED(hr))
		{
			selectedunitSchema[0] = unitSchemaArray[selectedUnit];
			unitIdCount = 1;
			displayUnits(
				_T("Selected Sensor"),
				selectedunitSchema,
				unitSchemaCount
				);
	
			WinBioFree(unitSchemaArray);
			unitSchemaArray = NULL;
			selectedunitSchema = NULL;
			unitSchemaCount = 0;
		}
	}
</code>
**4.** **enrollNewUser()** -  If the app is in enroll mode, we can begin enrollment process by calling a native method. It ensures there is a biometric unit selected in the private sensor pool. If no unit is available the enroll process is aborted. Otherwise the process waits for a command to capture fingerprint and a finger is placed on the sensor.

<code>

	void onEnrollOrPractice(__in bool CommitEnrollment,__in SIZE_T selectedUnit)
	{
		WINBIO_UNIT_ID unitIdArray[1] = {};
		SIZE_T unitIdCount = ARRAYSIZE(unitIdArray);
	
		WINBIO_UNIT_SCHEMA *unitSchemaArray = NULL;
		SIZE_T unitSchemaCount = 0;
		HRESULT hr = WinBioEnumBiometricUnits(
			WINBIO_TYPE_FINGERPRINT,
			&unitSchemaArray,		&unitSchemaCount
			);
		if (SUCCEEDED(hr))
		{
			// Build the array of Unit IDs that will make
			// up the private pool
			unitIdArray[0] = unitSchemaArray[selectedUnit].UnitId;
			unitIdCount = 1;
	
			WinBioFree(unitSchemaArray);
			unitSchemaArray = NULL;
			unitSchemaCount = 0;
	
			WINBIO_UUID privateDatabaseId = PRIVATE_POOL_DATABASE_ID;
			WINBIO_SESSION_HANDLE sessionHandle = NULL;
			hr = WinBioOpenSession(
				WINBIO_TYPE_FINGERPRINT,
				WINBIO_POOL_PRIVATE,
				WINBIO_FLAG_BASIC,
				unitIdArray,
				unitIdCount,
				&privateDatabaseId,
				&sessionHandle
				);
			if (SUCCEEDED(hr))
			{
				WINBIO_BIOMETRIC_SUBTYPE subFactor = WINBIO_SUBTYPE_NO_INFORMATION;
				if (selectSubFactorMenu(&subFactor) &&
					subFactor != WINBIO_SUBTYPE_NO_INFORMATION &&
					subFactor != WINBIO_SUBTYPE_ANY)
				{
					//
					// Locate sensor
					//
					displayMessage(_T("\nTap the sensor once when you're ready to begin enrolling...\n\n"));
					WINBIO_UNIT_ID unitId = 0;
					hr = WinBioLocateSensor(sessionHandle, &unitId);
					if (SUCCEEDED(hr))
					{
						//
						// Enroll begin
						//
						displayMessage(_T("\n(Begining enrollment sequence)\n\n"));
						hr = WinBioEnrollBegin(
							sessionHandle,
							subFactor,
							unitId
							);
						if (SUCCEEDED(hr))
						{
							SIZE_T swipeCount = 0;
							for (swipeCount = 1;; ++swipeCount)
							{
								wchar_t *sCount = (swipeCount == 1) ? _T("the first") : _T("another") ;
								displayMessage(
									_T("Swipe your finger on the sensor to capture ") + (sCount) + _T(" sample.\n")
									);
	
								WINBIO_REJECT_DETAIL rejectDetail = 0;
								hr = WinBioEnrollCapture(
									sessionHandle,
									&rejectDetail
									);
								_tprintf(_T("   Sample %d captured from unit number %d.\n"), swipeCount, unitId);
								if (hr == WINBIO_I_MORE_DATA)
								{
									_tprintf(_T("   More data required.\n\n"));
									continue;
								}
								if (FAILED(hr))
								{
									if (hr == WINBIO_E_BAD_CAPTURE)
									{
										_tprintf(_T("*** Error - Bad capture.\n"));
										_tprintf(
											_T("- %s\n"),
											BioHelper::ConvertRejectDetailToString(rejectDetail)
											);
										hr = S_OK;
										continue;
									}
									else
									{
										break;
									}
								}
								else
								{
									_tprintf(_T("   Template completed.\n\n"));
									break;
								}
							}
							_tprintf(_T("\n"));
							
						}
					}
				}
				else
				{
					hr = E_INVALIDARG;
				}
	
				WinBioCloseSession(sessionHandle);
				sessionHandle = NULL;
			}
		}
	
		if (FAILED(hr))
		{
			LPTSTR errorMessage = BioHelper::ConvertErrorCodeToString(hr);
			_tprintf(_T("*** Error - %s\n"), errorMessage);
			LocalFree(errorMessage);
			errorMessage = NULL;
		}
	}

</code>

**5.** **captureUserFingerPrint()** - If the app is in enroll mode, the native code request the WBF to capture multiple fingerprint for each finger placed on the sensor. The WBF creates a map of the multiple to fingerprint to create a pattern. This map can be stored in a database as a template for use during identification.

<code>

	void onEnrollOrPractice(__in bool CommitEnrollment,__in SIZE_T selectedUnit)
	{
		...
		if (SUCCEEDED(hr))
		{
			if (CommitEnrollment)
			{
				// ENROLL
				WINBIO_IDENTITY identity = {};
				BOOLEAN isNewTemplate = FALSE;

				_tprintf(_T("Committing enrollment...\n"));
				hr = WinBioEnrollCommit(sessionHandle, &identity, &isNewTemplate);
				if (SUCCEEDED(hr))
				{
					_tprintf(_T("Enrollment committed to database\n"));
					displayIdentity(&identity, subFactor);
				}
			}
			else
			{
				// PRACTICE
				_tprintf(_T("Discarding enrollment...\n"));
				hr = WinBioEnrollDiscard(sessionHandle);
				if (SUCCEEDED(hr))
				{
					_tprintf(_T("Template discarded.\n"));
				}
			}
		}
		...
	}


</code>

**6.** **getFingerPrintAsImage()** - To improve user experience we can use the fingerprint map to generate a image as a Bitmap format(bmp). The process of getting an image from the fingerprint image is discussed in detail here. 

<code>

	HRESULT CaptureSample()
	{
		... Open session ...

			hr = WinBioCaptureSample(
				sessionHandle,
				WINBIO_NO_PURPOSE_AVAILABLE,
				WINBIO_DATA_FLAG_RAW,
				&unitId,
				&sample,
				&sampleSize,
				&rejectDetail
				);

			if (FAILED(hr))
			{
				if (hr == WINBIO_E_BAD_CAPTURE)
					std::cout << "Bad capture; reason: " << rejectDetail << "\n";
				else
					std::cout << "WinBioCaptureSample failed.hr = 0x" << std::hex << hr << std::dec << "\n";

				if (sample != NULL)
				{
					WinBioFree(sample);
					sample = NULL;
				}

				if (sessionHandle != NULL)
				{
					WinBioCloseSession(sessionHandle);
					sessionHandle = NULL;
				}

				return hr;
			}

			std::cout << "Swipe processed - Unit ID: " << unitId << "\n";
			std::cout << "Captured " << sampleSize << " bytes.\n";

			if (sample != NULL)
			{
				PWINBIO_BIR_HEADER BirHeader = (PWINBIO_BIR_HEADER)(((PBYTE)sample) + sample->HeaderBlock.Offset);
				PWINBIO_BDB_ANSI_381_HEADER AnsiBdbHeader = (PWINBIO_BDB_ANSI_381_HEADER)(((PBYTE)sample) + sample->StandardDataBlock.Offset);
				PWINBIO_BDB_ANSI_381_RECORD AnsiBdbRecord = (PWINBIO_BDB_ANSI_381_RECORD)(((PBYTE)AnsiBdbHeader) + sizeof(WINBIO_BDB_ANSI_381_HEADER));

				DWORD width = AnsiBdbRecord->HorizontalLineLength; // Width of image in pixels
				DWORD height = AnsiBdbRecord->VerticalLineLength; // Height of image in pixels

				std::cout << "Image resolution: " << width << " x " << height << "\n";

				PBYTE firstPixel = (PBYTE)((PBYTE)AnsiBdbRecord) + sizeof(WINBIO_BDB_ANSI_381_RECORD);

				SBmpImage bmp;
				std::vector<uint8> data(width * height);
				memcpy(&data[0], firstPixel, width * height);

				SYSTEMTIME st;
				GetSystemTime(&st);
				std::stringstream s;
				s << st.wYear << "." << st.wMonth << "." << st.wDay << "." << st.wHour << "." << st.wMinute << "." << st.wSecond << "." << st.wMilliseconds;
				std::string bmpFile = "data/fingerPrint_" + s.str() + ".bmp";

				BmpSetImageData(&bmp, data, width, height);
				BmpSave(&bmp, bmpFile);
				//ShellExecuteA(NULL, NULL, bmpFile.c_str(), NULL, NULL, SW_SHOWNORMAL);

				CFile raw("rawData.bin");
				raw.write(&data[0], data.size());
				raw.close();

				WinBioFree(sample);
				sample = NULL;
			}

			if (sessionHandle != NULL)
			{
				WinBioCloseSession(sessionHandle);
				sessionHandle = NULL;
			}
		}
		return hr;
	}

</code>

**7.** **identifyUser()** - if the app is in identify mode, this method request native code to verify if  the fingerprint placed on sensor is already enrolled. First it ensures there is a biometric unit in our private sensor then put in mode to capture a sample fingerprint. The sample is verified against pattern maps stored in our database. if user was identified the native code return data of the fingerprint stored.

<code>
	
	void onIdentifyOrDelete(__in bool DeleteEnrollment,__in SIZE_T selectedUnit)
	{
		... opening session ...
		if (SUCCEEDED(hr))
		{
			_tprintf(_T("\nIdentify yourself by swiping your finger on the sensor...\n"));

			WINBIO_UNIT_ID unitId = 0;
			WINBIO_REJECT_DETAIL rejectDetail = 0;
			WINBIO_BIOMETRIC_SUBTYPE subFactor = WINBIO_SUBTYPE_NO_INFORMATION;
			WINBIO_IDENTITY identity = {};

			hr = WinBioIdentify(
				sessionHandle,
				&unitId,
				&identity,
				&subFactor,
				&rejectDetail
				);
			_tprintf(_T("\n- Swipe processed - Unit ID: %d\n"), unitId);
			if (FAILED(hr))
			{
				if (hr == WINBIO_E_UNKNOWN_ID)
				{
					_tprintf(_T("\n- Unknown identity.\n"));
					hr = S_OK;
				}
				else if (hr == WINBIO_E_BAD_CAPTURE)
				{
					_tprintf(_T("\n- Bad capture.\n"));
					_tprintf(
						_T("- %s\n"),
						BioHelper::ConvertRejectDetailToString(rejectDetail)
						);
					hr = S_OK;
				}
			}
			else
			{
				// Display identity
				displayIdentity(&identity, subFactor);

				// Delete identity
				if (DeleteEnrollment)
				{
					_tprintf(_T("\nDeleting template...\n"));
					hr = WinBioDeleteTemplate(
						sessionHandle,
						unitId,
						&identity,
						subFactor
						);
					if (SUCCEEDED(hr))
					{
						_tprintf(_T("\n- Template removed from private database.\n"));
					}
				}
			}
			WinBioCloseSession(sessionHandle);
			sessionHandle = NULL;
		}
	}

	if (FAILED(hr))
	{
		LPTSTR errorMessage = BioHelper::ConvertErrorCodeToString(hr);
		_tprintf(_T("*** Error - %s\n"), errorMessage);
		LocalFree(errorMessage);
		errorMessage = NULL;
	}
	}
</code>

#### Compiling and Testing the code ####
compile the C++ program to generate DLL file to be imported as native java library. 

#### Conclusion ####
