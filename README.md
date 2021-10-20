using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.IO;

namespace WindowsFormsApp2
{
    public partial class Form1 : Form
    {
        int[,] mat;
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            StreamReader sr;
            string riga = "";
            string[] dati;

            if (File.Exists("punti.txt"))
            {
                mat = new int[5, 5];
                dgV_matrice1.RowCount = 4;
                dgV_matrice1.ColumnCount = 4;
                foreach (DataGridViewColumn column in dgV_matrice1.Columns)
                {

                    column.HeaderText = String.Concat("Partita ",
                        (column.Index + 1).ToString());
                }

                foreach (DataGridViewRow row in dgV_matrice1.Rows)
                {

                    row.HeaderCell.Value = String.Concat("Giocatore ",
                        (row.Index + 1).ToString());
                }

                dgV_matrice1.RowHeadersWidth = 100;     //largh. colonna titoli
                dgV_matrice1.ColumnHeadersHeight = 100;
                sr = new StreamReader("punti.txt");

                for (int i = 0; i < 4 && !sr.EndOfStream; i++){
                    riga = sr.ReadLine();

                    if (!string.IsNullOrEmpty(riga))
                    {
                            dati = riga.Split(';');
                            for (int j = 0; j < 4; j++)
                            {
                                mat[i, j] = Convert.ToInt32(dati[j]);

                                dgV_matrice1.Rows[i].Cells[j].Value = mat[i, j];
                            }
                        }
                    }
                
                sr.Close();
                dgV_matrice1.ClearSelection();
            }

        }

         private int CalcolaTotalePunti(int index, bool riga)
        {
            int somma = 0;
            if (riga)                      //Somma riga
                for (int i = 0; i < 4; i++)
                    somma += mat[index, i];
            else                          //somma colonna
                
                    for (int i = 0; i < 4; i++)
                        somma += mat[i, index];
             return somma;
        }
        
        private int RicercaMax()
        {
            int max = 0;
            int indexmax = -1;      //indice giocatore che ha fatto più punti
            for (int i = 0; i < 4; i++)
                if (max < mat[i, 4])
                {
                    max = mat[i, 4];
                    indexmax = i;
                }

            return indexmax;
        }

        private int MaxPuntiPartita(int indexgiocatore)
        {
            int max = 0;
            int indexmax = -1;      //indice partita in cui il giocatore che ha fatto più punti
            for (int i = 0; i < 4; i++)
                if (max < mat[indexgiocatore, i])
                {
                    max = mat[indexgiocatore, i];
                    indexmax = i;
                }
            return indexmax;
        }

        private void button1_Click(object sender, EventArgs e)
        {
            for (int i = 0; i < 4; i++)     //TOT PUNTI x GIOCATORE
                mat[i, 4] = CalcolaTotalePunti(i,true);

            for (int i = 0; i < 4; i++)      //TOT PUNTI x PARTITA
                mat[4, i] = CalcolaTotalePunti(i,false);

            int indexgiocatore = RicercaMax();

            int indexPartita = MaxPuntiPartita(indexgiocatore);

            string nome = String.Concat("Partita n°: ", (indexPartita+1).ToString());
            nome += "\nTotale punti: " + mat[4, indexPartita].ToString();
            MessageBox.Show(nome);

            //Salvare su file
            using (StreamWriter sw = new StreamWriter("risultato.txt"))
                sw.WriteLine(nome);
        }
    }
}
