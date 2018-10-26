
Следующую команду, чтобы указать построение выпуска решения **SOLUTION_FILE.sln** для iPhone. Расположение IPA-ФАЙЛ можно задать, указав `IpaPackageDir` свойства в командной строке:

 - На компьютере Mac с помощью **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

**Xbuild** команды обычно находится в каталоге **/Library/Frameworks/Mono.framework/Commands**.

 - В Windows, с помощью **msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**MSBuild** не будет автоматически расширяться `$( )` выражения, переданный с помощью командной строки. По этой причине рекомендуется использовать полный путь при задании `IpaPackageDir` в командной строке.


См. в разделе [заметки о выпуске для iOS 9.8](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location) Дополнительные сведения о `IpaPackageDir` свойство.
