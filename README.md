# git-demo
A simple demo
public static void main(String[] args) throws Exception {
        GenerateJksFromURL sample = new GenerateJksFromURL();
        String url = "https://tcp.digitsecure.com/cpgw1.2/CpgwProcessor";
        String path = "/home/malleswar/Desktop/";
        String certName = "CpGateway";
        String keyStorePwd = "digit@123";
        boolean decision = true;
        sample.testConnectionTo(url, path + certName);
        StringBuilder command = new StringBuilder();
        command.append("keytool -importcert -trustcacerts -file ");
        command.append(path).append(certName).append(".crt -alias domain2 -keystore ");
        command.append(path).append(certName).append(".jks");
        sample.executeCommand(String.valueOf(command), keyStorePwd, decision);
    }

    private void executeCommand(String command, String keyStorePwd, boolean decision) {
        String s;
        Process p;
        try {
            System.out.println("command:" + command);
            p = Runtime.getRuntime().exec(command);
            final BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(p.getOutputStream()));
            writer.write(keyStorePwd);
            writer.newLine();
            writer.write(keyStorePwd);
            writer.newLine();
            if (decision) {
                writer.write("yes");
            } else {
                writer.write("No");
            }
            writer.newLine();
            writer.close();
            BufferedReader br = new BufferedReader(new InputStreamReader(p.getErrorStream()));
            while ((s = br.readLine()) != null)
                System.out.println("line: " + s);
            p.waitFor();
            System.out.println("exit: " + p.exitValue());
            p.destroy();
        } catch (Exception e) {
            e.printStackTrace();

        }
    }

    public void testConnectionTo(String url, String filePath) throws Exception {
        URL destinationURL = new URL(url);
        HttpsURLConnection conn = (HttpsURLConnection) destinationURL.openConnection();
        conn.connect();
        Certificate[] certs = conn.getServerCertificates();
        PEMWriter pemWrt = new PEMWriter(new FileWriter(filePath + ".crt"));
        for (Certificate cert : certs) {
            pemWrt.writeObject(cert);
        }
        System.out.println("@@ cERTIFICATE GENERATION COMPLETED");
        pemWrt.close();
    }


command:
==================
keytool -importcert -trustcacerts -file /home/malleswar/Desktop/CpGateway.crt -alias domain2 -keystore /home/malleswar/Desktop/CpGateway.jks
