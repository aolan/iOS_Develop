# xcodeproj

https://github.com/CocoaPods/Xcodeproj

> 需求：使用脚本自动拉取资源，添加到工程目录中。

> 解决方案：最终找到xcodeproj，这个ruby的开源程序，Cocoapods就是采用这个工具来修改工程项目的。


不说废话，直接贴代码，关键信息已经替换掉：

```ruby


#!/usr/bin/ruby

require 'xcodeproj'
require 'fileutils'
require "open-uri"  
require 'json'
require 'pathname'
require 'zip'


# 获取最新生产包的下载链接
def get_download_url_request(uri)
	data_response = nil;
	open(uri) do |http|  
  		data_response = http.read;
	end
	if data_response == nil then
		return nil;
	end
	data = JSON.parse(data_response)['data'];
	if data == nil then
		return nil;
	end
	download_url = JSON.parse(data_response)['data']['downloadUrl'];
	puts "最新的RN代码下载链接为：#{download_url}";
	return download_url;
end

# 下载RN代码
def download_newest_rn_code(download_url, file_dir, file_name)
	# 下载文件
	data_response = nil;
	open(download_url) do |http|  
  		data_response = http.read;
	end 
	# 写入到文件
	path = File.join(file_dir, file_name);
	puts "写入到文件：#{path}";
	aFile = File.new(path, 'w+');
	if aFile then
		aFile.syswrite(data_response);
		puts "写入文件成功!!!";
  	else
		puts "创建文件失败!!!";
	end
	# 解压文件
	puts "解压文件：#{path}";
	Zip::File.open(path) do |zipfile|
  		zipfile.each do |entry|
    		unless File.exist?(entry.name)
    			entry_path = File.join(file_dir, entry.name);
    			FileUtils::mkdir_p(File.dirname(entry_path));
    			zipfile.extract(entry, entry_path) unless File.exist?(entry_path);
      		end
    	end
  	end
  	# 删除zip文件
  	puts "删除zip文件：#{path}";
  	FileUtils.rm_r path;
end


# 递归删除group目录下的所有资源文件
def removeBuildPhaseFilesRecursively(aTarget, aGroup)
	aGroup.files.each do |file|
  		aTarget.resources_build_phase.remove_file_reference(file);
	end
	aGroup.groups.each do |group|
	  removeBuildPhaseFilesRecursively(aTarget, group);
	end
end

# 删除bundles目录下所有引用
def removeBundlesReference(aProject, aGroup)
	group = aProject.main_group.find_subpath(aGroup, false);
	if !group.empty? then
		aProject.targets.each do |target|
			if target.to_s == 'test-dev' || target.to_s == 'test-release' || target.to_s == 'test-appstore' then
				removeBuildPhaseFilesRecursively(target, group);
			end
		end
		group.clear();
		aProject.save();
	end
end

# 删除目录下所有文件
def deleteDirectory(dirPath)
	if Dir.exist?dirPath then
		FileUtils.rm_r dirPath;		
	end
end

# copy最新的代码到工程目录
def copyCodeAndResources(src, dest)
	FileUtils.cp_r src, dest;
	puts "删除解压出来的临时文件";
	deleteDirectory(src);
end


# 添加项目引用
def addBundlesReference(aProject, aGroup, folder_dir)
	group = aProject.main_group.find_subpath(aGroup, false);
	puts "遍历新的bundles目录，目录下有：\n"
	Dir.foreach(folder_dir) do |entry|
		if entry != "." and entry != '..' then
			puts entry;
			file_ref = group.new_reference(entry);
			aProject.targets.each do |target|
				if target.to_s == 'test-dev' || target.to_s == 'test-release' || target.to_s == 'test-appstore' then
					target.resources_build_phase.add_file_reference(file_ref);
				end
			end
		end
	end
	aProject.save();
end


##########################################################脚本入口

def update_rn_code()

	# 参数配置
	# 工程根目录
	project_root_path = File.expand_path('../../.') ;
	# 脚本所在目录
	script_path = Pathname.new(File.dirname(__FILE__)).realpath;
	# xcodeproj项目路径
	xcodeproj_path = "#{project_root_path}/test/test.xcodeproj";
	# RN代码物理路径
	rn_folder_path = "#{project_root_path}/test/test/src/common/rn/bundles"
	# 获取线上环境更新链接API
	download_url_api = "http://test.com/rncode";
	# RN代码相对group，这个是试出来的，很麻烦
	rn_group_path = "test/src/common/rn/bundles";
	# 下载下来的文件命名
	download_file_name = "CodePush";
	# 下载下来的文件类型
	download_file_extension = ".zip";

	puts "\n";
	puts "从CodePush上拉最新的RN代码...";
	puts "\n";
	puts "******************相关配置******************";
	puts "项目根目录：#{project_root_path}";
	puts "脚本路径：#{script_path}";
	puts "xcodeproj文件路径：#{xcodeproj_path}";
	puts "rn代码物理路径：#{rn_folder_path}";
	puts "获取下载链接API：#{download_url_api}";
	puts "******************相关配置******************";
	puts "\n";

	# 读取xcodeproj文件
	project = Xcodeproj::Project.open(xcodeproj_path);


	# 获取最新生产包的下载链接
	puts "\n"
	puts "1. 获取最新生产包的下载链接\n";
	download_url = get_download_url_request(download_url_api);

	unless download_url then
		puts "\n"
		puts "******************悲伤的故事******************";
		puts "获取最新生产包的下载链接失败，放弃更新RN代码.";
		puts "******************悲伤的故事******************";
		return nil;
	end

	# 下载RN代码
	puts "\n"
	puts "2. 下载RN代码\n";
	download_newest_rn_code(download_url, script_path, "#{download_file_name}#{download_file_extension}");

	# 删除bundles目录下所有引用
	puts "\n"
	puts "3. 删除bundles目录下所有引用\n";
	removeBundlesReference(project, rn_group_path);

	# 删除bundles目录下所有文件
	puts "\n"
	puts "4. 删除bundles目录下所有文件\n";
	deleteDirectory(rn_folder_path);

	# 复制最新的代码和资源到项目目录
	puts "\n"
	puts "5. 复制最新的代码和资源到项目目录\n";
	copyCodeAndResources(File.join(script_path, download_file_name), rn_folder_path);

	# 添加项目引用
	puts "\n"
	puts "6. 添加项目引用\n";
	addBundlesReference(project, rn_group_path, rn_folder_path);

	puts "\n"
	puts "******************喜大普奔******************";
	puts "更新RN代码成功";
	puts "******************喜大普奔******************";

end

##########################################################脚本入口








```


