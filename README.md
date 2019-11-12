# Lev R
feettomet = 1.0/3.2808399; % 1.0/0.264
depstep1=uint16(0);
xlen=uint16(0);
ind=uint16(0);
ftype=uint16(0);
fver=uint16(0);
blocksize=uint16(0);
fdummy1=uint32(0);
flags=uint16(0);
lowlim=single(0);
watertemp=single(0);
waterspeed=single(0);
firstgps=uint8(0);
yUTM=uint32(0);
xUTM=uint32(0);
surfacedep=single(0);
topofbottomdep=single(0);
timeoffset=uint32(0);
speedGPS=single(0);
track=single(0);
altitude=single(0);
datalen=uint16(0);
%depth=single(0);
Dscat=zeros(0,0); % формирует массив нулей размера 0 х 0.
Mscat=zeros(0,0); % формирует массив нулей размера 0 х 0.
fileName='C:\CHART124.SLG';
fileID = fopen(fileName);
finfo=dir(fileName); 
fsize=finfo.bytes;
type=fread(fileID,[1,1],'uint16');
ver=fread(fileID,[1,1],'uint16');
blocksize=fread(fileID,[1,1],'uint16');
dummy1=fread(fileID,[1,1],'uint32');
reccount=uint64(0);
datcount=uint64(0);
datcount=1;
while (~feof(fileID)) && (ftell(fileID)<fsize)

reccount=reccount+1;
flags(datcount)=fread(fileID,[1,1],'uint16');
lowlim(datcount)=fread(fileID,[1,1],'single');

depth(datcount)=fread(fileID,[1,1],'single');
if (bitand(flags(datcount),16))
    watertemp(datcount)=fread(fileID,[1,1],'single');
end
if (bitand(flags(datcount),128))
   waterspeed(datcount)=fread(fileID,[1,1],'single');
end
if (bitand(flags(datcount),256))
    firstgps=1;
    yUTM(datcount)=fread(fileID,[1,1],'uint32');
    xUTM(datcount)=fread(fileID,[1,1],'uint32');
end
if (bitand(flags(datcount),2048))
    surfacedep(datcount)=fread(fileID,[1,1],'single');
    topofbottomdep(datcount)=fread(fileID,[1,1],'single');
end  
if (bitand(flags(datcount),8192))
   timeoffset(datcount)=fread(fileID,[1,1],'uint32');
end
if (bitand(flags(datcount),16384))
    speedGPS(datcount)=fread(fileID,[1,1],'single');
    track(datcount)=fread(fileID,[1,1],'single');
    altitude(datcount)=fread(fileID,[1,1],'single');
end
datalen(datcount)=fread(fileID,[1,1],'uint16');
xlen=datalen(datcount)+1.0;
%depstep1(datcount)=lowlim(datcount)./xlen;
Dscat=fread(fileID,[datalen(datcount),1],'uint8');
Mscat=Dscat(:,datcount);    % матрица каждого элемента Dscat;
fshift(reccount)=10+blocksize*reccount;    
ind(reccount)=fseek(fileID,fshift(reccount),'bof');  
    if (bitand(flags(datcount),256)) ...
       &&(bitand(flags(datcount),2048))&&(bitand(flags(datcount),8192))
        datcount=datcount+1;
    end  
    Wscat=Mscat(80:1920,:);   % матрица на 1840 эл.
    [pks,i,g] = findpeaks(Wscat);
    % plot3(i,pks,z,'or')    % построение графика максимумов 
 
end
tdepth=-feettomet.*depth';
tdepstep1=feettomet.*depstep1;
ttopofbottomdep=-feettomet.*topofbottomdep';
tlowlim=feettomet.*lowlim; 
fclose(fileID);
