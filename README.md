# MATLAB-Project
 AN IMAGE-BASED FACIAL EXPRESSION RECOGNITION SYSTEM USING  LDA CLASSIFIER
% this is the code for Facial Expression in perceptual colorspace
clc;
close all;
clear all;
warning off;
%--------------------------------------------------------------------------



%Set the target folders for feeding training data
F=dir('Jaffe_images');
F=char(F.name);
nfldrs=size(F,1)-2;%No. of folders(Expressions)
%--------------------------------------------------------------------------

%ff=[0,2,4,8,16,32];%scaling factor
theta = [pi/3,pi/6,pi/2,3*(pi/4)];  %No. of angles(orientations) for the filter 
%Sx=2;%x for the filter
%Sy=4;%y for the filter
h=waitbar(0,'Please wait! The module is being trained');%small notification 
%--------------------------------------------------------------------------



for f=1:nfldrs
    if(f == 1)
        currentDir = strcat('Jaffe_images\',F(f+2,:));
    else
        currentDir = strcat(F(f+2,:));
    end
    
        
    cd(currentDir)
    imagefiles = dir('*.TIFF');
    
    nfiles = length(imagefiles); 
    
    for ii=1:nfiles
        currentfilename = imagefiles(ii).name;
        I = imread(currentfilename);
        images{ii} = I;
        
        I=imresize(I,[256 256]);%Resize to a uniform frame
        I=double(I);   
         [vec,count,m,svec]=facefind(I,[],[],[],[],[]);%Use the facefind filter
         Fimg=imcrop(uint8(I),[vec(1) vec(3) (vec(2)-vec(1)) (vec(4)-vec(3))]);%keep the face image only  
%         
        for tt = 1:length(theta)
            gabout{tt}=gabor_fn(Fimg,0.50*2.25,theta(tt),2.25,0,0.5);
            gabout{tt}=imresize(gabout{tt},[256 256]);
            
            %E(tt)=entropy(gabout{tt});
        end
                gaboutsum=gabout{1};
                for k=2:length(theta)
                    gaboutsum=gaboutsum+gabout{k};
                end
        %idx=find(max(E)==E);
        fv{f,ii}=gaboutsum;
        
    end
    waitbar(f/nfldrs);
    cd ..
    
%     for ii=1:nimgs
%         newText = strcat('testing\',F(f+2,:));
%         
%         cd(newText)
%         
%         imageSrc = strcat(num2str(ii),'.jpg');
%         I=imread(imageSrc);%read image in the folder
%         I=imresize(I,[256 256]);%Resize to a uniform frame
%         cd ..
%         cd ..      
%         I=double(I);
%         
% %         if strcmp(newText,'testing\surprise')
% %             if strcmp(imageSrc,'5.jpg')
% %             comute =0;
% %             end
% %         end
%         
%         [vec,count,m,svec]=facefind(I(:,:,1),91,[],[],[],[]);%Use the facefind filter
%         Fimg=imcrop(uint8(I),[vec(1) vec(3) (vec(2)-vec(1)) (vec(4)-vec(3))]);%keep the face image only  
%         
%         for tt = 1:length(theta)
%             gabout{tt}=gabor_fn(Fimg(:,:,1),0.50*2.25,theta(tt),2.25,0,1);
%             E(tt)=entropy(gabout{tt});
%         end
%         idx=find(max(E)==E);
%         
%         
%         % for kk=1:length(ff)
%         %    for tt=1:length(theta)
%         %[G,gabout{kk,tt}] = gaborfilter(Fimg(:,:,1),Sx,Sy,ff(kk),theta(tt));
%         
%         fv{f,ii}=gabout{idx};
%         %fvq=gabor_fn(Fimg(:,:,1),0.50*2.25,(pi/2),2.25,0,1);
%         %E(kk*tt)=entropy(gabout{kk,tt});        
%          %   end
%         %end
%         %idx=find(max(E)==E);
%         %fv{f,ii}=gabout{idx};     
% %     fv{f,ii}=gabout;
%     end
%     waitbar(f/nfldrs)
end
close(h);
cd ..
save fv fv
