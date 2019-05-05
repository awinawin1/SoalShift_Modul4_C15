# SoalShift_Modul4_C15
### Risky Aswi Narni(05111740000014) C-15
## soal1
Semua nama file dan folder harus terenkripsi
Enkripsi yang Atta inginkan sangat sederhana, yaitu Caesar cipher. Namun, Kusuma mengatakan enkripsi tersebut sangat mudah dipecahkan. Dia menyarankan untuk character list diekspansi,tidak hanya alfabet, dan diacak. Berikut character list yang dipakai:

qE1~ YMUR2"`hNIdPzi%^t@(Ao:=CQ,nx4S[7mHFye#aT6+v)DfKL$r?bkOGB>}!9_wV']jcp5JZ&Xl|\8s;g<{3.u*W-0`
membuat fungsi enkrip dan dekrip, untuk enkrip : suku kata tersebut + key% sumlah suku kata list
					dekrip : suku kata tersebut - key % jumlah suku kat list


```void enc(char* encrypt)
{
	if(!strcmp(encrypt,"..") || !strcmp(encrypt,".")) return;
	int enkripsi = strlen(encrypt);
	for(int i=0;i<enkripsi;i++)
	{
		for(int j=0;j<94;j++)
		{
			if(encrypt[i]==list[j])
			{
				encrypt[i] = list[(j+17)%94];
				break;
			}
		}
	}
}

void dec(char* decrypt)
{
	if(!strcmp(decrypt,".") || !strcmp(decrypt,"..")) return;
	int dekripsi = strlen(decrypt);
	for(int i=0;i<dekripsi;i++)
	{
		for(int j=0;j<94;j++){
			if(decrypt[i]==list[j]){
				decrypt[i] = list[(j+77)%94];
				break;
			}
		}
	}
}

```
## soal2
Semua file video yang tersimpan secara terpecah-pecah (splitted) harus secara otomatis tergabung (joined) dan diletakkan dalam folder “Videos”
Urutan operasi dari kebutuhan ini adalah:
Tepat saat sebelum file system di-mount
Secara otomatis folder “Videos” terbuat di root directory file system
Misal ada sekumpulan file pecahan video bernama “computer.mkv.000”, “computer.mkv.001”, “computer.mkv.002”, dst. Maka secara otomatis file pecahan tersebut akan di-join menjadi file video “computer.mkv”
Untuk mempermudah kalian, dipastikan hanya video file saja yang terpecah menjadi beberapa file. File pecahan tersebut dijamin terletak di root folder fuse
Karena mungkin file video sangat banyak sehingga mungkin saja saat menggabungkan file video, file system akan membutuhkan waktu yang lama untuk sukses ter-mount. Maka pastikan saat akan menggabungkan file pecahan video, file system akan membuat 1 thread/proses(fork) baru yang dikhususkan untuk menggabungkan file video tersebut
Pindahkan seluruh file video yang sudah ter-join ke dalam folder “Videos”
Jangan tampilkan file pecahan di direktori manapun
Tepat saat file system akan di-unmount
Hapus semua file video yang berada di folder “Videos”, tapi jangan hapus file pecahan yang terdapat di root directory file system
Hapus folder “Videos” 


fuse init harus diimplementasikan sendiri pada program fuse untuk melakukan permintaan sebelum file system di mount.

```static void xmp_init(struct fuse_conn_info *conn){
	char videos[1000] = "Videos";
	enc(videos);
	enc(youtuber);
	enc(filemiris);
	enc(iz1);
	sprintf(videosPath, "%s/%s", dirpath, videos);
	mkdir(videosPath, 0777);
	counter = 0;
	DIR* dp;
	struct dirent *de;
	dp = opendir(dirpath);
	while((de = readdir(dp)) != NULL){
		char temp[1000];
		sprintf(temp, "%s", de->d_name);
		dec(temp);
		printf("init temp %s\n", temp);
		if (isVideo(temp)){
			char namadepan[1000];
			int len = strlen(temp);
			sprintf(namadepan, "%s", temp);
			namadepan[len - 4] = '\0';
			pthread_create(&(buruhVideo[counter++]), NULL, &gabungVideo, (void *)namadepan);
		}
	}
	printf("%d\n", counter);
	return;
}

```
fungsi gabungVideo merupakan fungsi yang akan diberikan kepada sebuah thread untuk menggabungkan video yang sudah dinyatakan benar. Fungsi gabungVideo mengambil sebuah parameter yaitu namadepan dari sebuah file pecahan.


```void *gabungVideo(void *param){
	DIR *dp;
	struct dirent *de;
	char *namadepan = (char *) param;
	dp = opendir(dirpath);
	char newVideo[1000];
	char encVideo[1000];
	sprintf(encVideo, "%s", namadepan);
	enc(encVideo);
	sprintf(newVideo, "%s/%s", videosPath, encVideo);
	creat(newVideo, 0777);
	printf("gabung video %s\n", newVideo);
	while((de = readdir(dp)) != NULL){
		char temp[1000];
		sprintf(temp, "%s", de->d_name);
		dec(temp);
		printf("part file %s compare %s \n", temp, namadepan);
		if (strstr(temp, namadepan)){
			FILE *fp = fopen(newVideo, "a+");
			printf("part file status ok");
			char namafile[1000];
			sprintf(namafile, "%s/%s", dirpath, de->d_name);
			printf(" opening %s file and begin to copying ", namafile);
			FILE *fp2 = fopen(namafile, "r");
			
			size_t n, m;
			unsigned char buff[8192];
			do {
					n = fread(buff, 1, sizeof buff, fp2);
					if (n) m = fwrite(buff, 1, n, fp);
					else   m = 0;
			} while ((n > 0) && (n == m));
			
			fclose(fp2);
			fclose(fp);
			printf("\nsucces copying %s\n", temp);
		}
	}
	printf("gabung video selesai\n");
}
```
fungsi isVideo yang digunakan untuk memeriksa apakah sebuah file merupakan video. Intinya fungsi ini memeriksa apakah namafile yang diperiksa ber ekstensi .000
```

int isVideo(const char *path){
	char temp[1000];
	sprintf(temp, "%s", path);
	int len = strlen(temp);
	char *ext;
	ext = &temp[len - 4];
	if (strcmp(ext, ".000") == 0){
		return 1;
	}
	return 0;
}

```

## soal3
Sebelum diterapkannya file system ini, Atta pernah diserang oleh hacker LAPTOP_RUSAK yang menanamkan user bernama “chipset” dan “ic_cvoid enc(char* encrypt) controller” serta group “rusak” yang tidak bisa dihapus. Karena paranoid, Atta menerapkan aturan pada file system ini untuk menghapus “file bahaya” yang memiliki spesifikasi:
Owner Name     : ‘chipset’ atau ‘ic_controller’
Group Name    : ‘rusak’
Tidak dapat dibaca
Jika ditemukan file dengan spesifikasi tersebut ketika membuka direktori, Atta akan menyimpan nama file, group ID, owner ID, dan waktu terakhir diakses dalam file “filemiris.txt” (format waktu bebas, namun harus memiliki jam menit detik dan tanggal) lalu menghapus “file bahaya” tersebut untuk mencegah serangan lanjutan dari LAPTOP_RUSAK.
1. adduser:chipset &ic_controller
2. addgroup: rusak
```
if (strcmp(pwd->pw_name, "ic-controller") == 0 || strcmp(pwd->pw_name, "chipset") == 0){
		if (strcmp(grp->gr_name, "rusak") == 0){
			if (access(path, R_OK) != 0){
				ans = 1;
			}
```
karena root:root, maka diubah dulu dengan chown chipset:rusak atau chown ic_controller:rusak [namafile],
maka id dan grou akan berubah, dan di remove pindah di filemiris.txt.
ketika di chmod 0000 [namafile] maka akan tidak bisa dibaca dan di remove pindah di filemiris.txt.



```void eksekusiBahaya(const char *path, const char* name){
	struct stat stat;
	lstat(path, &stat);
	struct passwd *pwd = getpwuid(stat.st_uid);
	struct group *grp = getgrgid(stat.st_gid);
	int ans = 0;
	if (strcmp(pwd->pw_name, "ic-controller") == 0 || strcmp(pwd->pw_name, "chipset") == 0){
		if (strcmp(grp->gr_name, "rusak") == 0){
			if (access(path, R_OK) != 0){
				ans = 1;
			}
		}
	}
	printf("eksekusi berbahaya %s %s %s %d\n", path, pwd->pw_name, grp->gr_name, ans);
	if (ans == 1){
		FILE *fp;
		struct tm *tm;
		char buf[200];
		time_t t = stat.st_atime;
		tm = localtime(&t);
		strftime(buf, sizeof(buf), "%d.%m.%Y %H:%M:%S", tm);
		char filemirispath[1000];
		sprintf(filemirispath, "%s/%s", dirpath, filemiris);
		printf("filemirispath %s\n", filemirispath);
		fp = fopen(filemirispath, "a");
		fprintf(fp, "%s %s %s %s\n", name, grp->gr_name, pwd->pw_name, buf);
		fclose(fp);
		remove(path);
	}
}

```

## soal4
Pada folder YOUTUBER, setiap membuat folder permission foldernya akan otomatis menjadi 750. Juga ketika membuat file permissionnya akan otomatis menjadi 640 dan ekstensi filenya akan bertambah “.iz1”. File berekstensi “.iz1” tidak bisa diubah permissionnya dan memunculkan error bertuliskan “File ekstensi iz1 tidak boleh diubah permissionnya.”

ketika ada folder YOUTUBER Dan didalamnya terdapat file2 dan direktori/folder maka namanama file dan folder akan bertambah ekstensi .iz1 di belakangnya
```
static int xmp_getattr(const char *path, struct stat *stbuf)
{
  int res;
	char fpath[1000];
	char name[1000];
	sprintf(name,"%s",path);
	enc(name);
	sprintf(fpath, "%s%s",dirpath,name);
	printf("getattr custom %s\n", fpath);
	if (strstr(fpath, youtuber)){
		char newNamePath[1000];
		int len = strlen(fpath);
		strcpy(newNamePath, fpath);
		newNamePath[len - 4] = '\0';
		char *ext = &fpath[len - 4];
		if (strcmp(ext, iz1) == 0){
			res = lstat(newNamePath, stbuf);
		}else{
			res = lstat(fpath, stbuf);
		}
	}else{
		res = lstat(fpath, stbuf);
	}
	if (res != 0){
		return -ENOENT;
	}

	return 0;
}

```
ketika folder dapat dirubah permissionnya dan untuk file tidak dapat drubah permissionnya

```

static int xmp_chmod(const char *path, mode_t mode)
{
	int res;
	char fpath[1000];
	char name[1000];
	sprintf(name,"%s",path);
	enc(name);
	sprintf(fpath, "%s%s",dirpath,name);
	struct stat statt;
	lstat(fpath, &statt);
	if (strstr(fpath, youtuber) && S_ISREG(statt.st_mode)){
		pid_t child = fork();
		int status;
		if (child == 0){
			char *argv[5] = {"zenity", "--error", "--title=Pesan error", "--text=File ekstensi iz1 tidak boleh diubah permissionnya.", NULL};
			execv("/usr/bin/zenity", argv);
		}else{
			while (wait(&status) > 0);
		}
	}else{
		res = chmod(fpath, mode);	
	}
	if (res == -1)
		return -errno;

	return 0;
}
static int xmp_mkdir(const char *path, mode_t mode)
{
	int res;
	char fpath[1000];
	char name[1000];
	sprintf(name,"%s",path);
	enc(name);
	sprintf(fpath, "%s%s",dirpath,name);
	printf("mkdir %s\n", fpath);
	if (strstr(fpath, youtuber)){
		res = mkdir(fpath, 0750);
	}else{
		res = mkdir(fpath, mode);
	}
	if (res == -1)
		return -errno;

	return 0;
}

static int xmp_create(const char* path, mode_t mode, struct fuse_file_info* fi) {
	(void) fi;

	int res;
	char fpath[1000];
	char name[1000];
	sprintf(name,"%s",path);
	enc(name);
	sprintf(fpath, "%s%s",dirpath,name);
	printf("create %s\n", fpath);
	if (strstr(fpath, youtuber)){
		res = creat(fpath, 0640);
	}else{
		res = creat(fpath, mode);
	}
	if(res == -1)
		return -errno;

	close(res);
	return 0;
}
```
## soal5
Ketika mengedit suatu file dan melakukan save, maka akan terbuat folder baru bernama Backup kemudian hasil dari save tersebut akan disimpan pada backup dengan nama namafile_[timestamp].ekstensi. Dan ketika file asli dihapus, maka akan dibuat folder bernama RecycleBin, kemudian file yang dihapus beserta semua backup dari file yang dihapus tersebut (jika ada) di zip dengan nama namafile_deleted_[timestamp].zip dan ditaruh ke dalam folder RecycleBin (file asli dan backup terhapus). Dengan format [timestamp] adalah yyyy-MM-dd_HH:mm:ss

xmp_unlink fungsi untuk ketika file yang dihapus maka akan dibuatkan folder RecycleBin
Kemudian file yang dihapus beserta semua backup dari file yang dihapus tersebut di zip dan dimasukkan dalam RecycleBin 
```static int xmp_unlink(const char *path)
{
	char fpath[1000], temp[1000];
	strcpy(temp, path);
	enc(temp);

	if(strcmp(temp,"/") == 0)
	{
		path=dirpath;
		sprintf(fpath,"%s",path);
	}
	else sprintf(fpath, "%s%s",dirpath,temp);

	int res, status;

	if(access(fpath, F_OK)<0)			
		return 0;

	char soal[1000], soal2[1000], waktu[100], file_zip[1000], fileasli[1000], ext[1000],
			namafile[1000];
	
	char fd_recyclebin[100]="/home/awin/revisimod4/oO.k.EOX[)";
	mkdir(fd_recyclebin, 0777);

	int posisi_ext = posisi(path, '.');
	
	if (posisi_ext==0)
		posisi_ext = strlen(path);
	else{
		strcpy(ext, path+posisi_ext);
	}
	strcpy(fileasli, path);
	enc(fileasli);	

	int posisi_slash = posisi(path, '/');
	int dot = posisi_ext-(posisi_slash+1);
	strncpy(namafile, path+1+posisi_slash, dot);
	namafile[dot] = '\0';

	time_t t = time(NULL);
	struct tm tm = *localtime(&t);
	sprintf(waktu, "%04d-%02d-%02d_%02d:%02d:%02d", tm.tm_year + 1900, tm.tm_mon + 1, tm.tm_mday, tm.tm_hour, tm.tm_min, tm.tm_sec);

	sprintf(file_zip, "%s_deleted_%s", namafile, waktu);
	char zip_temp[1000], file_zip_temp[1000]; 
	char zip[100]="`S[u";
	enc(file_zip);
	enc(namafile);
	
	sprintf(file_zip_temp, "%s.zip", file_zip);
	sprintf(zip_temp, "%s%s", file_zip, zip);

	char backup[100] = "XB.Jhu"; char recycle[100]= "oO.k.EOX[)";
	sprintf(soal, "cd %s && zip '%s/%s' '%s' '%s/%s'* && rm -f '%s/%s'*", dirpath, recycle, file_zip, fileasli, backup, namafile, backup, namafile);
	sprintf(soal2, "%s && cp '%s/%s' '%s/%s' && rm -f '%s/%s'", soal, recycle, file_zip_temp, recycle, zip_temp, recycle, file_zip_temp); 
	if (fork()==0)
		execlp("bash","bash", "-c", soal2, NULL);

		while((wait(&status))>0);


	res = unlink(fpath);
	
	if (res == -1)
		return -errno;

	return 0;
}
```
xmp_write() membuat folder backup
kita akan mendapatkan nama file saja dan ekstensinya saja disimpan di namafile
dan menyimpan waktu di var waktu
```
static int xmp_write(const char *path, const char *buf, size_t size,
		     off_t offset, struct fuse_file_info *fi)
{
	int fd;
	int res;
    char fpath[1000];
    char name[1000];
    char temp[1000];
	sprintf(name,"%s",path);
    enc(name);
	if(strcmp(path,"/") == 0)
	{
		path=dirpath;
		sprintf(fpath,"%s",path);
	}
	else sprintf(fpath, "%s%s",dirpath,name);
	
	(void) fi;
	fd = open(fpath, O_WRONLY);
	if (fd == -1)
		return -errno;

	res = pwrite(fd, buf, size, offset);
	if (res == -1)
		res = -errno;

	close(fd);
	
	strcpy(temp,path); enc(temp);
	sprintf(name, "%s/%s", dirpath,temp);
	
		if(access(name, R_OK)<0)				
		return res;
	
	char fd_backup[1000]="/home/awin/revisimod4/XB.Jhu";
	mkdir(fd_backup, 0777);
	
	char namafile[1000], ext[100], waktu[1000], filejadi[1000];
	
	int posisi_ext = posisi(path,'.');
	
	if (posisi_ext==0) {
		posisi_ext = strlen(path);
		ext[0] = '\0';
	}
	else{
		strcpy(ext, path+posisi_ext);
	}
	int posisi_slash = posisi(path, '/');
	int dot = posisi_ext-(posisi_slash+1);
	strncpy(namafile, path+1+posisi_slash, dot);
	namafile[dot] = '\0';
	
	time_t t = time(NULL);
	struct tm tm = *localtime(&t);
	sprintf(waktu, "%04d-%02d-%02d_%02d:%02d:%02d", tm.tm_year + 1900, tm.tm_mon + 1, tm.tm_mday, tm.tm_hour, tm.tm_min, tm.tm_sec);

	sprintf(filejadi, "%s_%s", namafile, waktu); 
	strcat(filejadi, ext);	
	enc(filejadi);
	sprintf(name, "%s%s", dirpath, temp);
	
	char final[1000];
	sprintf(final, "%s/%s", fd_backup, filejadi);

	int status;
	if (fork()==0)
		execlp("cp","cp", name, final, NULL);

	while((wait(&status))>0);
	
	return res;
}


```
