package TikeAssignment;

import java.io.*;
import java.util.*;
import java.util.regex.*;

import org.apache.tika.exception.TikaException;
import org.apache.tika.metadata.Metadata;
import org.apache.tika.parser.ParseContext;
import org.apache.tika.parser.pdf.PDFParser;
import org.apache.tika.sax.BodyContentHandler;
import org.xml.sax.ContentHandler;
import org.xml.sax.SAXException;


public class TikaHW {
	
//	private static final Detector DETECTOR = new DefaultDetector(
//	        MimeTypes.getDefaultMimeTypes());


	List<String> keywords;
	PrintWriter logfile;
	int num_keywords, num_files, num_fileswithkeywords;
	Map<String,Integer> keyword_counts;
	Date timestamp;

	List<Pattern> pattern_list;
	List<Matcher> matcher_list;
	
	String stringToSearch;
	/**
	 * constructor
	 * DO NOT MODIFY
	 */
	public TikaHW() {
		keywords = new ArrayList<String>();
		num_keywords=0;
		num_files=0;
		num_fileswithkeywords=0;
		keyword_counts = new HashMap<String,Integer>();
		timestamp = new Date();
		try {
			logfile = new PrintWriter("log.txt");
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		}
	}

	/**
	 * destructor
	 * DO NOT MODIFY
	 */
	protected void finalize() throws Throwable {
		try {
			logfile.close();
	    } finally {
	        super.finalize();
	    }
	}

	/**
	 * main() function
	 * instantiate class and execute
	 * DO NOT MODIFY

	 */
	public static void main(String[] args) throws IOException, TikaException, SAXException {
		TikaHW instance = new TikaHW();
		instance.run();
	}

	/**
	 * execute the program
	 * DO NOT MODIFY

	 */
	private void run() throws IOException, TikaException, SAXException {

		// Open input file and read keywords
		try {
			BufferedReader keyword_reader = new BufferedReader(new FileReader("keywords.txt"));
			String str;
			while ((str = keyword_reader.readLine()) != null) {
				keywords.add(str);
				num_keywords++;
				keyword_counts.put(str, 0);
			}
			keyword_reader.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
		
//####################################
		pattern_list= new ArrayList<Pattern>();
        for (String i : keywords){
        	pattern_list.add(Pattern.compile(" (\\S+"+i+"\\S+) ",Pattern.CASE_INSENSITIVE));
        }
        
        matcher_list= new ArrayList<Matcher>();
        for (Pattern p : pattern_list){
        	matcher_list.add(p.matcher(""));
        }
//####################################
        
		// Open all pdf files, process each one
		File pdfdir = new File("vault");
		File[] pdfs = pdfdir.listFiles(new PDFFilenameFilter());
		for (File pdf:pdfs) {
			num_files++;
			processfile(pdf);
		}

		// Print output file
		try {
			PrintWriter outfile = new PrintWriter("output.txt");
			outfile.print("Keyword(s) used: ");
			if (num_keywords>0) outfile.print(keywords.get(0));
			for (int i=1; i<num_keywords; i++) outfile.print(", "+keywords.get(i));
			outfile.println();
			outfile.println("No of files processed: " + num_files);
			outfile.println("No of files containing keyword(s): " + num_fileswithkeywords);
			outfile.println();
			outfile.println("No of occurrences of each keyword:");
			outfile.println("----------------------------------");
			for (int i=0; i<num_keywords; i++) {
				String keyword = keywords.get(i);
				outfile.println("\t"+keyword+": "+keyword_counts.get(keyword));
			}
			outfile.close();
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		}
	}

	/**
	 * Process a single file
	 * 
	 * Here, you need to:
	 *  - use Tika to extract text contents from the file
	 *  - (optional) check OCR quality before proceeding
	 *  - search the extracted text for the given keywords
	 *  - update num_fileswithkeywords and keyword_counts as needed
	 *  - update log file as needed
	 * 
	 * @param f File to be processed
	 */
	private void processfile(File f) throws IOException, TikaException, SAXException {
		num_files++;
		/***** YOUR CODE GOES HERE *****/
        PDFParser parser = new PDFParser();

        Metadata metadata = new Metadata();

        ParseContext parseContext = new ParseContext();
        ContentHandler handler = new BodyContentHandler();
        
        parser.parse(new FileInputStream(f),handler, metadata, parseContext);
        
        stringToSearch=handler.toString();

        Boolean flag=false;
        String key;
        int i=0;
        for (Matcher m : matcher_list){
	        if (m.reset(stringToSearch).find()) {
	          if (!flag)flag=true;
	          i= matcher_list.indexOf(m);
	          key=keywords.get(i);
	          keyword_counts.put(key, keyword_counts.get(key)+1);
	          updatelog(key,f.getName());
	        }
        }
        if(flag)num_fileswithkeywords++;

//        System.out.println(detected);
/*
        if(detected.equalsIgnoreCase("application/pdf") || metadata.get(metadata.CONTENT_TYPE).equalsIgnoreCase("application/pdf")){
       System.out.println("metadata: " + metadata.CONTENT_TYPE + " - " + metadata.get(metadata.CONTENT_TYPE));

       System.out.println("Content: " + handler.toString());

        System.out.println("newContent:"+ detected);
        }
*/
		// to update the log file with a search hit, use:
		// 	updatelog(keyword,f.getName());
	}

	/**
	 * Update the log file with search hit
	 * Appends a log entry with the system timestamp, keyword found, and filename of PDF file containing the keyword
	 * DO NOT MODIFY
	 */
	private void updatelog(String keyword, String filename) {
		timestamp.setTime(System.currentTimeMillis());
		logfile.println(timestamp + " -- \"" + keyword + "\" found in file \"" + filename +"\"");
		logfile.flush();
	}

	/**
	 * Filename filter that accepts only *.pdf
	 * DO NOT MODIFY
	 */
	static class PDFFilenameFilter implements FilenameFilter {
		private Pattern p = Pattern.compile(".*\\.pdf",Pattern.CASE_INSENSITIVE);
		public boolean accept(File dir, String name) {
			Matcher m = p.matcher(name);
			return m.matches();
		}
	}
}
