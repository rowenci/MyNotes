import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileStatus;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;
import org.apache.log4j.Logger;
import org.junit.Test;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.URI;


public class TestHdfs {

    private static Logger logger = Logger.getLogger(TestHdfs.class);

    /**
     * 连接至HDFS
     * @return
     */
    public FileSystem connectToHDFS() throws Exception{
        String NAME_NODE_URL = "hdfs://192.168.1.11:9000";
        String NAME_NODE_NAME = "fs.defaultFs";

        FileSystem fs = null;
        Configuration conf = new Configuration();
        try {
            //conf.set(NAME_NODE_NAME, NAME_NODE_URL);
            System.setProperty("HADOOP_USER_NAME", "root");
            fs = FileSystem.get(URI.create(NAME_NODE_URL), conf);
            logger.info("连接成功：Path={}"+fs.getFileStatus(new Path("/")));
        } catch (Exception e) {
            logger.error(e.getMessage(), e);
        }
        return fs;
    }

    /**
     * 创建目录
     * @throws Exception
     */
    @Test
    public void mkdirFolder() throws Exception {
        String folderName = "/HelloWorld";
        FileSystem fs = connectToHDFS();
        fs.mkdirs(new Path(folderName));
    }

    /**
     * 上传文件
     * @throws Exception
     */
    @Test
    public void uploadFile() throws Exception {
        FileSystem fs = connectToHDFS();
        //定义本地上传的文件路径
        String localFilePath = "D://Hadoop//upload//";
        //定义上传文件
        String fileName = "user.xlsx";
        //定义要上传到的文件夹
        String uploadFolder = "/input/";

        InputStream in = new FileInputStream(localFilePath + fileName);
        OutputStream out = fs.create(new Path(uploadFolder + fileName));

        IOUtils.copyBytes(in, out, 4096, true);

    }

    /**
     * 获取文件
     * @throws Exception
     */
    @Test
    public void getFileFromHadoop() throws Exception {
        FileSystem fs = connectToHDFS();
        //定义要下载路径
        String downloadPath = "/input/";
        //定义要下载的文件名
        String downloadFileName = "user.xlsx";
        //定义要保存的路径
        String savePath = "D://Hadoop//download//" + downloadFileName;

        InputStream in = fs.open(new Path(downloadPath + downloadFileName));
        OutputStream out = new FileOutputStream(savePath);
        IOUtils.copyBytes(in, out, 4096, true);
    }

    /**
     * 删除文件
     * @throws Exception
     */
    @Test
    public void deleteFile() throws Exception {
        FileSystem fs = connectToHDFS();
        //要删除的文件路径
        String deleteFilePath = "/inputuser.xlsx";
        Boolean deleteResult = fs.delete(new Path(deleteFilePath), true);
        logger.info("删除文件：={}"+deleteResult);
    }

    /**
     * 遍历指定目录下所有文件
     * @throws Exception
     */
    @Test
    public void getAllFile()throws Exception{
        FileSystem fs = connectToHDFS();
        //定义要获取的目录
        String getPath = "/";
        FileStatus[] statuses = fs.listStatus(new Path(getPath));
        for (FileStatus file: statuses
        ) {
            logger.info("fileName={}"+file.getPath().getName());
        }
    }
}
